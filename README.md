# Fallen
A simpler daemon library for Ruby processes.

## Usage

```ruby
require "fallen"

module Azazel
  extend Fallen
  
  def self.run
    while running?
      puts "Time is on my side... Yes it is..."
      sleep 666
    end
  end
end

Azazel.start!
```

This will print _Time is on my side... Yes it is.._ every 666 seconds on `STDOUT`. You can stop the daemon by pressing `CTRL+c`.

## Callbacks

`Fallen` includes four callback methods that are executed on starting and stopping the daemon. These are:

* `before_start`
* `after_start`
* `before_stop`
* `after_stop`

Override them in your script to add the desired functionality, like so:

```ruby
require "fallen"

module Azazel
  extend Fallen
  
  def self.run
    while running?
      # Do something
    end
  end
  
  def self.before_start
    puts "I'm just getting started!"
  end
  
  def self.before_stop
    puts "I'm about to stop. Bye!"
  end
end
```

## Control your daemon
`Fallen` accepts the following methods:

* `daemonize!`: detaches the process and keeps running it in the background;
* `chdir!`: changes the current working directory of the process (useful for log and PID files);
* `pid_file`: allows to indicate a file path where the daemon `PID` will be stored; could be either an absolute or a relative path to the current working directory (see `chdir!`);
* `stdout`, `stderr` & `stdin`: redirect the process `STDOUT`, `STDERR` and `STDIN` to either an absolute or relative file path; note that when a process is daemonized by default all these streams are redirected to `/dev/null`.

For example, the previous example could have the following lines before `Azazel.start!` to store the `PID` and log to a file:

```ruby
Azazel.pid_file "/var/run/azazel.pid"
Azazel.stdout "/var/log/azazel.log"
Azazel.daemonize!
Azazel.start!
```

## CLI support
`Fallen` supports command line parameters, by default using the [`clap`](https://github.com/soveran/clap) gem. In order to enable command line support you need to do the following:

```ruby
require "fallen"
require "fallen/cli"

module Azazel
  extend Fallen
  extend Fallen::CLI
  
  # ...
  
  def self.usage
    puts "..." # Your usage message
    # Default Fallen usage options
    puts fallen_usage
  end
end

case Clap.run(ARGV, Azazel.cli).first
when "start"
  Azazel.start!
when "stop"
  Azazel.stop!
else
  Azazel.usage
end
```

`Azazel.usage` will print the following:

```
Fallen introduced command line arguments:

  -D    Daemonize this process
  -C    Change directory.
  -P    Path to PID file. Only used in daemonized process.
  -out  Path to redirect STDOUT.
  -err  Path to redirect STDERR.
  -in   Path to redirect STDIN.
```

## License
See the `UNLICENSE` file included with this gem distribution.
