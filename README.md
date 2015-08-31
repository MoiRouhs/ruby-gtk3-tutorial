# Getting started with GTK+ with the ruby-gnome2 Gtk3 module.

This is a ruby adaptation of the official tutorial for the C language that you can find at https://developer.gnome.org/gtk3/stable/gtk-getting-started.html.

GTK+ is a widget toolkit. Each user interface created by GTK+ consists of widgets. The Gtk3 module of the ruby-gnome2 project is an implementation of the ruby bindings for GTK+.

With Gtk3, Widgets are organized in a hierachy. The Gtk::Window widget is the main container. The user interface is then built by adding buttons, drop-down menus, input fields, and other widgets to the window.

If you are creating complex user interfaces it is recommended to use Gtk::Builder and its GTK-specific markup description language, instead of assembling the interface manually. You can also use a visual user interface editor, like Glade.

GTK+ is event-driven. The toolkit listens for events such as a click on a button, and passes the event to your application.

Here is the most basic example that illustrate the principles of widget hierarchy and events management:

```ruby
require "gtk3"

window = Gtk::Window.new("First example")
window.set_request_size(400, 400)
window.set_border_width(10)

button = Gtk::Button.new(:label => "Say hello")
button.signal_connect "clicked" do |_widget|
  puts "Hello World!!"
end

window.add(button)
window.signal_connect("delete-event") { |_widget| Gtk.main_quit }
window.show_all

Gtk.main
```
This tutorial will mainly be focused on the use of Gtk::Application, which is the best way to create an application.

## Basics
https://developer.gnome.org/gtk3/stable/gtk-getting-started.html#id-1.2.3.5

To begin our introduction to GTK, we'll start with a simple signal-based Gtk application. This program will create an empty 200 × 200 pixel window.

*    example-0.rb
```ruby
require "gtk3"

app = Gtk::Application.new("org.gtk.example", :flags_none)

app.signal_connect "activate" do |application|
  window = Gtk::ApplicationWindow.new(application)
  window.set_title("Window")
  window.set_default_size(200, 200)
  window.show_all
end

puts app.run

```
When creating a Gtk::Application you need to pick an application identifier (a name) and input to `Gtk::Application#new` as parameter. For this example *org.gtk.example* is used but for choosing an identifier for your application see this [guide](https://wiki.gnome.org/HowDoI/ChooseApplicationID).
Lastly `Gtk::Application#new` takes a `Gio::ApplicationFlags` constants as input for your application, if your application would have special needs (those constants can be replaced by theirs respective symbol ie. `Gio::ApplicationFlags::NONE` == `:flags_none`).

Next we add instructions for the "activate" event of the `Gtk::Application` instance we created. The activate signal will be sent when your application is launched with the method `Gtk::Application#run` on the line below. This method also takes as arguments a ruby array of string. This allows GTK+ to parse specific command line arguments that control the behavior of GTK+ itself. The parsed arguments will be removed from the array, leaving the unrecognized ones for your application to parse.

Inside the "activate" event block, we want to construct our GTK window, so that a window is shown when the application is launched. The call to `Gtk::ApplicationWindow#new` will create a new `Gtk::Window`. The window will have a frame, a title bar, and window controls depending on the platform.

A window title is set using `Gtk::Window#set_title`. This function takes a string as input. Finally the window size is set using `Gtk::Window#set_default_size` and the window is then shown by GTK via `Gtk::Widget#show_all`.

When you exit the window, by for example pressing the X, the `Gtk::Application#run` in the main loop returns with a number which is the exit status.

While the program is running, GTK+ is receiving *events*. These are typically input events caused by the user interacting with your program, but also things like messages from the window manager or other applications. GTK+ processes these and as a result, signals may be emitted on your widgets. Connecting handlers for these signals is how you normally make your program do something in response to user input.
The following example is slightly more complex, and tries to showcase some of the capabilities of GTK+.

In the long tradition of programming languages and libraries, it is called *Hello, World*.
*    example-1.rb
```ruby
require "gtk3"
app = Gtk::Application.new("org.gtk.example", :flags_none)

app.signal_connect "activate" do |application|
  window = Gtk::ApplicationWindow.new(application)
  window.set_title("Window")
  window.set_default_size(200, 200)

  button_box = Gtk::ButtonBox.new(:horizontal)
  window.add(button_box)

  button = Gtk::Button.new(label: "Hello World")
  button.signal_connect "clicked" do |widget|
    puts "Hello World"
    window.destroy
  end

  button_box.add(button)

  window.show_all
end

# Gtk::Application#run need C style argv ([prog, arg1, arg2, ...,argn]).
# The ARGV ruby variable only contains the arguments ([arg1, arg2, ...,argb])
# and not the program name. We have to add it explicitly.

puts app.run([$0] + ARGV)
```
As seen above, example-1.rb builds further upon example-0.rb by adding a button to our window, with the label "Hello World". Two new variables are created to accomplish this, button and button_box.

The button_box variable stores a `Gtk::ButtonBox` object, which is GTK+'s way of controlling the size and layout of buttons. The `Gtk::ButtonBox` is created with the method `Gtk::ButtonBox#new` which takes a `Gtk::Orientation `constant as parameter or the related symbols (`:vertical` or `:horizontal`).

The buttons which this box will contain can either be stored horizontally or vertically but this does not matter in this particular case as we are dealing with only one button. After initializing button_box with horizontal orientation, the code adds the button_box widget to the window widget using `Gtk::ButtonBox#add`.

Next the button variable is initialized in similar manner. The method `Gtk::Button#new` is called which returns a GtkButton to be stored inside button. A label is set using a ruby hash as argument :`:label => "Hello World"`.

Afterwards button is added to our button_box. Using the method "Gtk::Button#signal_connect" we add instructions, so that when the button is clicked, a message will be displayed in the terminal if the GTK application was started from one.

After that, `Gtk::Window#destroy` is called. This method is herited from `Gtk::Widget`. This has the effect that when the button is clicked, the whole GTK window is destroyed. More information about creating buttons can be found [here](https://wiki.gnome.org/HowDoI/Buttons).
The rest of the code in example-1.rb is identical to example-0.rb. Next section will elaborate further on how to add several GtkWidgets to your GTK application.

## Packing
https://developer.gnome.org/gtk3/stable/ch01s02.html

*    example-2.rb

## Building user interfaces
https://developer.gnome.org/gtk3/stable/ch01s03.html

*    example-4.rb

## Building applications

### A trivial application
https://developer.gnome.org/gtk3/stable/ch01s04.html#id-1.2.3.12.5

*    exampleapp1.rb

### Populating the window
https://developer.gnome.org/gtk3/stable/ch01s04.html#id-1.2.3.12.6

*    exampleapp2.rb

### Opening files
https://developer.gnome.org/gtk3/stable/ch01s04.html#id-1.2.3.12.7

*    exampleapp3.rb

### An application menu
https://developer.gnome.org/gtk3/stable/ch01s04.html#id-1.2.3.12.8

*    exampleapp4.rb

### A preferences dialog
https://developer.gnome.org/gtk3/stable/ch01s04.html#id-1.2.3.12.9

*    exampleapp5.rb
*    exampleapp6.rb

### Adding a search bar
https://developer.gnome.org/gtk3/stable/ch01s04.html#id-1.2.3.12.10
