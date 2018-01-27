---
layout: article
title: Logging in NetBeans Platform
modified:
categories: pyrus
excerpt: How to set up logging in the NetBeans platforms with single line formatting and capturing timestamp, class, method and level of the invoked logging call.
tags: [java, netbeans, programming, snippet, netbeans_platform, logging]
image:
  feature: 
  teaser: teaser-netbeans-logging-400x250.jpg
  thumb:
comments: true
---

The Pyrus Suite is built upon the NetBeans platform which provides a large amount of plumbing for a typical application. One of the provided features is a logging environment that is based on the standard java.util.logging tools. This makes it trivially easy to add logging calls using the logger into your code e.g. <code>LOG.info("Message");</code>. 

All that is required is to add a line to enable the logging in each class that you want to use. The line must identify the class specifically, so I have added this line into my blank class template in Tools/Templates -- just replace "MyClass" with "$(name)" in the template.

{% highlight java %}
    /* == ENABLE LOGGING == */
    transient private static final java.util.logging.Logger LOG =
        java.util.logging.Logger.getLogger(MyClass.class.getName());
{% endhighlight %}

Once you have enabled the logging, you can then start to control the logging output. For example the verbosity can be increased or decreased, allowing you to include different levels of debugging information. This is considered a good practice as you can have more detailed information, perhaps using timing calls for benchmarking code at the FINER and FINEST levels, and high level information for the user at the INFO level. To control the verbosity of the logging output that is recorded, you simply need to change the logging level.

But where do I find this in my NetBeans platform application? The answer is to create your an Installer for the module, override the <code>restored()</code> method, and set the level of the logger applicable to your package.

{% highlight java %}
package logging.example;

import java.util.logging.Level;
import java.util.logging.Logger;
import org.openide.modules.ModuleInstall;

/**
 * This class is run during startup of the NetBeans platform.
 * @author Peter Kirkham
 */
public class Installer extends ModuleInstall {

    @Override
    public void restored() {
        
        /*
         * Put some code here on application startup. This is where any startup
         * code for all your application modules should go.
         */
        
        // First of all change the logging formatting for our Pyrus modules
        final SingleLineFormatter formatter = new SingleLineFormatter();
        
        // Establish logging level
        Logger example_root_logger = Logger.getLogger("logging.example");
        example_root_logger.setLevel(Level.FINER);

        // Set the root logger format and level
        Logger root_logger = Logger.getLogger(this.getClass().getName());
        while (root_logger.getParent() != null) {
            root_logger = root_logger.getParent();
        }
        root_logger.setLevel(Level.CONFIG);
    }
}
{% endhighlight %}

## Formatting Logging Output on a Single Line

This allows control of the logging level, but the formatting is another aspect that can be controlled. Standard logging output is to use two lines, but it is far easier to read if each logging message is output to a single line. Furthermore it is very useful if the time and calling class and method for the logging message can be identified (without having to manually insert it into the message for each and every log record). Fortunately the functionality to achieve this is built into the java.util.logging environment. What we need to do is create our own formatter, and then set this as the formatted for the NetBeans logging handler. All messages passed into the log are thus manipulated by the formatter to conform to our requirements.

{% highlight java %}
package logging.example;

import java.text.DateFormat;
import java.text.Format;
import java.text.MessageFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.logging.Formatter;
import java.util.logging.LogManager;
import java.util.logging.LogRecord;

/**
 * The SimpleFormatter tends to put things on two lines which just doesn't look right in the logs and makes it hard to
 * read. This puts is all on one line. A {@link Formatter} that may be customised in a {@code logging.properties} file.
 * The syntax of the property {@code au.com.newwavegeo.pyruscore.SingleLineFormatter.format} specifies the output. A
 * newline will be appended to the string and the following special characters will be expanded (case sensitive):
 * <ul>
 * <li>{@code %m} - message</li>
 * <li>{@code %L} - log level</li>
 * <li>{@code %n} - name of the logger</li>
 * <li>{@code %t} - timestamp (in "HH:mm:ss" format)</li>
 * <li>{@code %M} - source method name (if available, otherwise "?")</li>
 * <li>{@code %c} - source class name (if available, otherwise "?")</li>
 * <li>{@code %C} - source simple class name (if available, otherwise "?")</li>
 * <li>{@code %T} - thread ID</li>
 * </ul>
 * The default format is {@value #DEFAULT_FORMAT}. Curly brace characters are not allowed.
 *
 * @author Samuel Halliday
 * @author Peter Kirkham
 */
public class SingleLineFormatter extends Formatter {

    /**
     *
     */
    public SingleLineFormatter() {
        super();

        // load the format from logging.properties
        String propName = getClass().getName() + ".format";
        String format = LogManager.getLogManager().getProperty(propName);
        if (format == null || format.trim().length() == 0) {
            format = DEFAULT_FORMAT;
        }
        if (format.contains("{") || format.contains("}")) {
            throw new IllegalArgumentException("curly braces not allowed");
        }

        // convert it into the MessageFormat form
        format = format.replace("%L", "{0}")
                .replace("%m", "{1}")
                .replace("%M", "{2}")
                .replace("%t", "{3}")
                .replace("%c", "{4}")
                .replace("%T", "{5}")
                .replace("%n", "{6}")
                .replace("%C", "{7}")
                + "\n";
        message_format = new MessageFormat(format);
        Format[] formats_by_arg_index = message_format.getFormatsByArgumentIndex();
        needs_arg = new boolean[formats_by_arg_index.length];
        for (int i = 0; i < formats_by_arg_index.length; i++) {
            needs_arg[i] = format.contains("{" + i + "}");
        }
    }

    @Override
    public String format(LogRecord record) {
        String[] arguments = new String[8];

        // %L - Logging level
        if (needs_arg[0]) {
            arguments[0] = record.getLevel().toString();
        }

        // %m - Logging message
        if (needs_arg[1]) {
            String msg = record.getMessage();
            if (msg != null) {
                arguments[1] = MessageFormat.format(msg, record.getParameters());
            }

            // sometimes the message is empty, but there is a throwable
            if (arguments[1] == null || arguments[1].length() == 0) {
                Throwable thrown = record.getThrown();
                if (thrown != null) {
                    arguments[1] = thrown.getMessage();
                }
            }
        }

        // %M - Method called from (appears to be null most of the time in NetBeans without custom handler)
        if (needs_arg[2]) {
            String source;
            if (record.getSourceMethodName() != null) {
                StringBuilder sb = new StringBuilder(record.getSourceMethodName());
                sb.insert(0, ".");
                source = sb.toString();
            } else {
                source = "";
            }
            arguments[2] = source;
        } else {
            arguments[2] = "?";
        }

        // %t - Time logged
        if (needs_arg[3]) {
            Date date = new Date(record.getMillis());
            synchronized (date_format) {
                arguments[3] = date_format.format(date);
            }
        }

        // %c - Class called from
        if (needs_arg[4]) {
            arguments[4] = record.getSourceClassName();
        } else {
            arguments[4] = "?";
        }

        // %T - Thread called from
        if (needs_arg[5]) {
            arguments[5] = Integer.toString(record.getThreadID());
        }

        // %n - Logged name
        if (needs_arg[6]) {
            arguments[6] = record.getLoggerName();
        }

        // %C - Class called from (without package name) and revert to logger name if null
        if (needs_arg[7]) {
            String source;
            if (record.getSourceClassName() != null) {
                source = record.getSourceClassName();
            } else {
                source = record.getLoggerName();
            }
            if (source != null) {
                int start = source.lastIndexOf(".") + 1;
                if (start > 0 && start < source.length()) {
                    arguments[7] = source.substring(start);
                } else {
                    arguments[7] = source;
                }
            } else {
                arguments[7] = "?";
            }
        }

        synchronized (message_format) {
            return message_format.format(arguments);
        }
    }
    /* == DEFINE CONSTANTS == */
    private static final String DEFAULT_FORMAT = "%t [%C%M|%L]: %m";

    /* == DECLARE GLOBAL VARIABLES == */
    private final MessageFormat message_format;
    private final boolean[] needs_arg;
    private final DateFormat date_format
            = new SimpleDateFormat("HH:mm:ss");

    /* == ENABLE LOGGING == */
    transient private static final java.util.logging.Logger LOG
            = java.util.logging.Logger.getLogger(SingleLineFormatter.class.getName());

{% endhighlight %}

In our <code>Installer</code> module we now add the SingleLineFormatter to the root logger handlers. This is done by adding the following to the <code>restored()</code> method:

{% highlight java %}
        final SingleLineFormatter formatter = new SingleLineFormatter();
		for (final Handler handler : root_logger.getHandlers()) {
            
            // Actions to be taken on the root loggers
            handler.setFormatter(formatter);
        }
{% endhighlight %}

### Where Are My Methods?

The big issue that you'll face with this code (assuming your mileage with the NetBeans platform is similar to mine), is that the methods are not shown and simply display as 'null'. This is not very helpful. The problem appears to be fundamental and according to the <Code>LogRecord</code> documentation:

> Therefore, if a logging Handler wants to pass off a LogRecord to another thread, or to transmit it over RMI, and if it wishes to subsequently obtain method name or class name information it should call one of `getSourceClassName` or `getSourceMethodName` to force the values to be filled in.

So what we need to do is make sure that a call to `getSourceMethodName` is made before any other requests are made of the `LogRecord` by various handlers in the NetBeans platform. We can intercept our log messages by providing our own logging handler, and ensuring that this method call is made.

{% highlight java %}
package logging.example;

import java.util.logging.Handler;
import java.util.logging.LogRecord;

/**
 * Custom logging handler to intercept log messages and ensure that we have called the getSourceMethodName. This is
 * required to ensure that we actually record and preserve the method name before it is passed to another thread.
 * @author Peter Kirkham
 */
class CustomHandler extends Handler {

    public CustomHandler() {
    }

    @Override
    public void publish(LogRecord lr) {
        lr.getSourceMethodName();
    }

    @Override
    public void flush() {
    }

    @Override
    public void close() throws SecurityException {
    }
}
{% endhighlight %}

Finally we need to add one last piece of code to our `Installer` class to ensure that the handler is assigned to our log records. This was surprisingly difficult to achieve as there doesn't appear to be much documentation about this -- at least my Google efforts were not very productive. Either I am the only programmer that has come across this issue (unlikely), or others don't seem to care much about it (more probable). In any event, I thought it would be worth recording my findings on this blog post in case they are of use to someone else.

{% highlight java %}
        // Set a special handler for our modules
        CustomHandler custom_handler = new CustomHandler();
        example_root_logger.addHandler(custom_handler);
{% endhighlight %}

If everything is working then in your logs you should see messages that look like:

> 09:00:00 [MyClass.myMethod\|INFO]: Log message sent through the logger
  
Enjoy :-)