# Chapter 15. Interpreter 

```ruby

require 'find'

class Expression
end

class All < Expression
    def evaluate(dir)
        results =[]
        Find.find(dir) do |p|
            next if not File.file?(p)
            results << p
        end
        results
    end
end

class FileName < Expression 
    def initialize(pattern)
        @pattern=pattern
    end

    def evaluate(dir)
        Find.find(dir) do |p| 
            next if not File.file?(p)
            name = File.basename(p)
            results << p if File.fnmatch(@pattern, name)
        end
    end
end

class Bigger < Expression 
    def initialize(size)
        @size=size
    end

    def evaluate(dir)
        Find.find(dir) do |p| 
            next if not File.file?(p)
            name = File.basename(p)
            results << p if(File.size(p) > @size)
        end
    end
end


class Writable < Expression 
    def evaluate(dir)
        Find.find(dir) do |p| 
            next if not File.file?(p)
            name = File.basename(p)
            results << p if(File.writable?(p))
        end
    end
end


expr_all = All.new 
files = expr_all.evaluate('*.mp3')

```

```ruby
class Not < Expression 
    def initialize(exp)
        @expression = exp 
    end
    def evaluate(dir)
        All.new.evaluate(dir) - @expression.evaluate(dir)
    end
end


expr_not_writable = Not.new( Writable.new )
```

```ruby
class Or < Expression
    def evaluate(dir)
        results1 = @expression1.evaluate(dir)
        results2 = @expression2.evaluate(dir)
        (results1 + results2).sort.uniq
    end
end

bog_or_mp3 = Or.new(Bigger.new(1024), FileName.new('*.mp3'))
```


## Buiold Parser



# Chapter 14. Builder 

## problem 

```ruby
class Computer 
    attr_accessor :display 
    attr_reader :motherboard 
    attr_reader :drivers
    def initalize(display=:crt, motherboard=Motherboard.new, drivers=[])
        @motherboard = motherboard
        @drivers = drivers
        @display = display
    end
end

class CPU 
end

class BasicCPU 
end

class TurboCPU 
end

class Motherboard
    attr_accessor :cpu 
    attr_accessor :memory_size 
    def initalize(cpu=BasicCPU.new, memory_size=1000)
        @cpu = cpu 
        @memory_size = memory_size
    end
end
```

## Builder

```ruby 
class ComputerBuilder
    attr_reader :computer 
    def initalize
        @computer = Computer.new 
    end
    def turbo(has_turbo_cpu=true)
        @computer.motherboard.cpu = TurboCPU.new 
    end
    def display=(display)
        @computer.display=display
    end
    def memory_size=(size_in_mb)
        @computer.motherboard.memory_size = size_in_mb
    end
    def add_cd(writer=false)
        @computer.drives << Driver.new(:cd, 760, writer)
    end
end

builder =ComputerBuilder.new 
builder.turbo 
builder.add_cd(true)
```

## Polymorphic Builders

```ruby
class DesktopBuilder < ComputerBuilder 
    def initalize
        @computer = DesktopComputer.new
    end
    def display=(display)
        @display = display
    end
end

class LaptopBuilder < ComputerBuilder 
    def initalize
        @computer = DesktopComputer.new
    end
    def display=(display)
        @display = display
    end
end
```

## Builder Can ensure Sane Object

# Chapter 13. Factory 

## The Problem 

```ruby
class Duck 

  def initialize(name)
    @name = name 
  end


  def eat  
    p "Duck #{@name} is eating"
  end

  def speak 
    p "Duck #{@name} say quak"
  end

  def sleep 
    p "Duck #{@name} sleeps quitly"
  end

end

class Frog 

  def initialize(name)
    @name = name 
  end


  def eat  
    p "Frog #{@name} is eating"
  end

  def speak 
    p "Frog #{@name} say Croooak"
  end

  def sleep 
    p "Frog #{@name} sleeps quitly"
  end
end


class Pond
  def initialize(number_ducks)
    @ducks = []
    number_ducks.times do |i|
      duck = Duck.new("Duck#{i}")
      @ducks << duck 
    end
  end

  def simulate_one_day 
    @ducks.each {|duck| duck.speak}
    @ducks.each {|duck| duck.eat}
    @ducks.each {|duck| duck.sleep}
  end
end

pond = Pond.new(3)
pond.simulate_one_day
```


## Template Method Strikes Again 

```ruby
class Pond
  def initialize(number_animals)
    @animals = []
    number_animals.times do |i|
      animal = new_animal("Duck#{i}")
      @animals << animal 
    end
  end

  def simulate_one_day 
    @animals.each {|animal| animal.speak}
    @animals.each {|animal| animal.eat}
    @animals.each {|animal| animal.sleep}
  end
end

class DuckPond < Pond 
  def new_animal(name)
    Duck.new(name)
  end
end

class FrogPond < Pond 
  def new_animal(name)
    Frog.new(name)
  end
end

pond = FrogPond.new(3)
pond.simulate_one_day
```

```ruby
class Pond
  def initialize(number_animals, number_plants)
    @animals = []
    number_animals.times do |i|
      animal = new_animal("Duck#{i}")
      @animals << animal 
    end

    @plants = []
    number_plants.times do |i| 
      plant = new_plant("Plant#{i}")
      @plants << plant
  end

  def simulate_one_day 
    @plants.each {|plant| plant.grow}
    @animals.each {|animal| animal.speak}
    @animals.each {|animal| animal.eat}
    @animals.each {|animal| animal.sleep}
  end
end


class DuckWaterLilyPond < Pond 
  def new_animal(name)
    Duck.new 
  end

  def new_plant 
    WaterLily.new
  end
end


class FrogAlgaePond < Pond 
  def new_animal(name)
    Frog.new 
  end

  def new_plant 
    Algae.new
  end
end



class Algae 
  def initialize(name)
    @name = name 
  end

  def grow 
    p "The algaer #{name} grow"
  end

end

class WaterLily 
  def initialize(name)
    @name = name 
  end

  def grow 
    p "The WaterLily #{name} grow"
  end

end

```



# Chapter 12. Singleton


## Class Variables

```ruby
class ClassVariableTester
  @@class_count = 0 
  def initialize 
    @instance_count = 0
  end

  def increment
    @@class_count = @@class_count + 1 
    @instance_count = @instance_count + 1
  end

  def to_s 
    "class_count #{@@class_count} instance_count: #{@instance_count}"
  end
end

c1 = ClassVariableTester.new 
c1.increment
c1.increment
c1.increment
c1.increment
p c1.to_s

c2 = ClassVariableTester.new 
c2.increment
c2.increment
p c2.to_s
```

## First Try

```ruby
class SimpleLogger
  attr_accessor :level 
  ERROR = 1 
  WARINING = 2
  INFO = 3

  def initialize
    @log = File.open("log.txt","w")
    @level = WARINING
  end

  def error(msg)
    @log.puts(msg)
    @log.flush 
  end

  def warning(msg)
    @log.puts(msg) if @level >= WARINING 
    @log.flush 
  end

  def info(msg)
    @log.puts(msg) if @level >= INFO 
    @log.flush 
  end
end

logger = SimpleLogger.new 
logger.level = SimpleLogger::INFO 

logger.info('Doing the frst')
logger.info('Doing the 123')
```


```ruby
class SimpleLogger

  @@instance = SimpleLogger.new 

  def self.instance 
    return @@instance 
  end

end

logger1 = SimpleLogger.instance
logger2 = SimpleLogger.instance
```

## Making sure THere is Only One

```ruby
class SimpleLogger

  @@instance = SimpleLogger.new 

  def self.instance 
    return @@instance 
  end

  private_class_method :new

end

logger = SimpleLogger.new
```

## The Singleton module

```ruby
require 'singleton'

class SimpleLogger
  include Singleton

end

logger = SimpleLogger.new
logger1 = SimpleLogger.instance
logger2 = SimpleLogger.instance
```

## Global Variables as Singleton

```ruby
$logger = SimpleLogger.new
```

## Classes As Singleton

```ruby
class ClassBasedLogger
  ERROR = 1
  WARINING = 2
  INFO = 3
  @@log = File.open('log.txt', 'w')
  @@level = WARINING

  def self.error(msg)
    @@log.puts(msg)
    @@log.flush 
  end

  def self.warining(msg)
    @@log.puts(msg) if @@level >= WARINING
    @@log.flush 
  end


  def self.info(msg)
    @@log.puts(msg) if @@level >= INFO
    @@log.flush 
  end

  def self.level=(new_level)
    @@level = new_level 
  end

  def self.level 
    @@level 
  end
end

ClassBasedLogger.level = ClassBasedLogger::INFO 
ClassBasedLogger.info('123')
```

## Moudles As Singleton 

```ruby
module ModuleBasedLogger
  ERROR = 1
  WARINING = 2
  INFO = 3
  @@log = File.open('log.txt', 'w')
  @@level = WARINING

  def self.error(msg)
    @@log.puts(msg)
    @@log.flush 
  end

  def self.warining(msg)
    @@log.puts(msg) if @@level >= WARINING
    @@log.flush 
  end


  def self.info(msg)
    @@log.puts(msg) if @@level >= INFO
    @@log.flush 
  end

  def self.level=(new_level)
    @@level = new_level 
  end

  def self.level 
    @@level 
  end
end

ClassBasedLogger.level = ClassBasedLogger::INFO 
ClassBasedLogger.info('123')
```

## Singleton Golang

```golang
package main

import (
	"fmt"
	"sync"
)

var lock = &sync.Mutex{}

type single struct {
}

var singleInstance *single

func getInstance() *single {
	if singleInstance == nil {
		lock.Lock()
		defer lock.Unlock()
		if singleInstance == nil {
			fmt.Println("Creating single instance now.")
			singleInstance = &single{}
		} else {
			fmt.Println("Single instance already created.")
		}
	} else {
		fmt.Println("Single instance already created.")
	}

	return singleInstance
}


func main() {

	for i := 0; i < 30; i++ {
		go getInstance()
	}

	// Scanln is similar to Scan, but stops scanning at a newline and
	// after the final item there must be a newline or EOF.
	fmt.Scanln()
}
```

# Chapter 11. Decorator

## Decorators: The Cur for Ugly Code

```ruby
class EnhancedWriter
  attr_reader :check_sum

  def initialize(path)
    @file = File.open(path, "w")
    @check_sum = 0 
    @line_number = 1
  end

  def write_line(line)
    @file.print(line)
    @file.print("\n")
  end

  def checksumming_write_line(data)
    data.each_byte {|byte| @check_sum = (@check_sum + byte) % 256}
    @check_sum += "\n"[0] % 256 
    write_line(data)
  end

  def timestamping_write_line(data)
    write_line("#{Time.new}: #{data}")
  end

  def numbering_write_line(data)
    write_line("%{@line_number}: #{data}")
    @line_number += 1
  end

  def close 
    @file.cloas
  end

end
```


```ruby
class SimpleWriter
  def initialize(path)
    @file  = File.open(path, 'w')
  end
  
  def write_line(line) 
    @file.print(line)
    @file.print("\n")
  end

  def pos
    @file.pos
  end

  def rewind 
    @file.rewind 
  end

  def close
    @file.close 
  end
end


class WriterDecorator 
  def initialize(real_writer)
    @real_writer = real_writer
  end

  def write_line(line)
    @real_writer.write_line(line)
  end

  def pos
    @real_writer.pos 
  end

  def rewind 
    @real_writer.rewind 
  end

  def close
    @real_writer.close 
  end

end

class NumberingWriter < WriterDecorator 
  def initialize(real_writer)
    super(real_writer)
    @line_number =  1
  end

  def write_line(line)
    @real_writer.write_line("#{@line_number}: #{line}")
    @line_number += 1 
  end
end

class CheckSummingWriter < WriterDecorator 
  attr_reader :check_sum 

  def initialize(real_writer)
    @real_writer = real_writer 
    @check_sum = 0
  end

  def write_line(line)
    data.each_byte {|byte| @check_sum = (@check_sum + byte) % 256}
    @check_sum += "\n"[0] % 256 
    write_line(data)
  end
end

class TimeStampingWriter < WriterDecorator 
  def write_line(line)
    @real_writer.write_line("#{Time.new}: #{line}")
  end
end

writer = NumberingWriter.new(SimpleWriter.new('final.text'))
writer.write_line('Hello out there')

writer = CheckSummingWriter.new(SimpleWriter.new('final.text'))
writer.write_line('Hello out there')

writer = TimeStampingWriter.new(SimpleWriter.new('final.text'))
writer.write_line('Hello out there')


writer = CheckSummingWriter.new(TimeStampingWriter.new(NumberingWriter.new(SimpleWriter.new('final.txt')))).new 
writer.write_line('Hello out there')
```

## Easing Delegation Blues 

```ruby
require 'forwardable'

class WriterDecorator 
  extend Forwardable 
  def_delegators :@real_writer, :write_line, :rewind, :pos, :close
  def initialize(real_writer)
    @real_writer = real_writer
  end
end
```

## Moduling 

```ruby
module NumberingWrite
  def initialize(real_writer)
    super(real_writer)
    @line_number =  1
  end

  def write_line(line)
    @real_writer.write_line("#{@line_number}: #{line}")
    @line_number += 1 
  end
end

module TimeStampingWriter 
  def write_line(line)
    @real_writer.write_line("#{Time.new}: #{line}")
  end
end

w = SimpleWriter.new('out')
w.extend(NumberingWriter)
w.extend(TimeStampingWriter)
w.write_line('hello')
```

## Decorator on GO

```golang
package main

import "fmt"

//Интерфейс компонента
type pizza interface {
	getPrice() int
}

//Конкретный компоненет

type veggeMania struct {
}

func (p *veggeMania) getPrice() int {
	return 15
}

// Конкретный декоратор

type tomatoTopping struct {
	pizza pizza
}

func (c *tomatoTopping) getPrice() int {
	pizzaPrice := c.pizza.getPrice()
	return pizzaPrice + 7
}

// Конкретный декоратор

type cheeseTopping struct {
	pizza pizza
}

func (c *cheeseTopping) getPrice() int {
	pizzaPrice := c.pizza.getPrice()
	return pizzaPrice + 10
}

// Клинсткий код

func main() {

	pizza := &veggeMania{}

	//Add cheese topping
	pizzaWithCheese := &cheeseTopping{
		pizza: pizza,
	}

	//Add tomato topping
	pizzaWithCheeseAndTomato := &tomatoTopping{
		pizza: pizzaWithCheese,
	}

	fmt.Printf("Price of veggeMania with tomato and cheese topping is %d\n", pizzaWithCheeseAndTomato.getPrice())
}
```


# Chapter 10. Proxy 

```ruby
# The Real object
class BankAccount 
  attr_reader :balance 

  def initialize(starting_balance=0)
    @balance = starting_balance
  end

  def deposit(amount)
    @balance += amount 
  end

  def withdraw(amount)
    @balance -= amount 
  end
end

#Present exactly the same intreface
class BankAccountProxy
  def initialize(real_object)
    @real_object = real_object
  end

  def balance
    @real_object.balance
  end

  def deposit(amount)
    @real_object.deposit(amount)
  end

  def withdraw(amount)
    @real_object.withdraw(amount)
  end
end


account = BankAccount.new(100)
account.deposit(50)
account.withdraw(10)

proxy = BankAccountProxy.new(account)
proxy.deposit(50)
proxy.withdraw(10)
```

## The protection proxy

```ruby

require 'etc'

class AccountProtectionProxy 
  def initialize(real_account, owner_name)
    @subject = real_account
    @owner_name = owner_name
  end

  def deposit
    check_access 
    return @subject.deposit(amount)
  end

  
  def withdraw
    check_access 
    return @subject.withdraw(amount)
  end

  def balance
    check_access 
    return @subject.balance(amount)
  end

  private 

  def check_access
    if Etc.getlogin != @owner_name
      raise "Illlegal access: #{Etc.getlogin}"
    end
  end

end
```

## Remote proxy

## Virtual Proxy

Delay creating expensive object, until we really need them.  

```ruby
class VirtalAccountProxy 
  def initialize(starting_balance=0)
    @balance = starting_balance
  end

  def deposit
    s = subject 
    return s.deposit(amount)
  end

  def withdraw
    s = subject 
    return s.withdraw(amount)
  end

  def balance
    s = subject 
    return s.balance
  end

  private 

  def subject 
    @subject || (@subject = BankAccount.new(@starting_balance))
  end
end

v = VirtalAccountProxy.new(100)
```

## Eliminating That Proxy Drudgery

```ruby
class TestMethodMissing
  def hello
    p ' hello from real method'
  end

  def method_missing(name, *args)
    p "unknown method #{name}"
    p "args: #{args.join(' ')}"
  end

end

tmm = TestMethodMissing.new
tmm.hello 
tmm.goodbuy('curel', 'world', test: false)
tmm.send(:hello)
tmm.send(:goodbye, 'cruel', 'world')

```

## Prixes without the Tears

```ruby
# The Real object
class BankAccount 
  attr_reader :balance 

  def initialize(starting_balance=0)
    @balance = starting_balance
  end

  def deposit(amount)
    @balance += amount 
  end

  def withdraw(amount)
    @balance -= amount 
  end
end


class AccountProxy 
  def initialize(real_account)
    @subject=real_account
  end

  def method_missing(name, *args)
    p name 
    @subject.send(name, *args)
  end
end

ap = AccountProxy.new( BankAccount.new(100))
ap.deposit(25)
ap.withdraw(50)
ap.balance
```

## Golang Proxy

```golang

package main

import "fmt"

//Subject


type server interface {
	handleRequest(string, string) (int, string)
}

//proxy
type nginx struct {
	application       *application
	maxAllowedRequest int
	rateLimiter       map[string]int
}

func newNginxServer() *nginx {
	return &nginx{
		application:       &application{},
		maxAllowedRequest: 2,
		rateLimiter:       make(map[string]int),
	}
}

func (n *nginx) handleRequest(url, method string) (int, string) {
	allowed := n.checkRateLimiting(url)
	if !allowed {
		return 403, "Not Allowed"
	}
	return n.application.handleRequest(url, method)
}

func (n *nginx) checkRateLimiting(url string) bool {
	if n.rateLimiter[url] == 0 {
		n.rateLimiter[url] = 1
	}
	if n.rateLimiter[url] > n.maxAllowedRequest {
		return false
	}
	n.rateLimiter[url] = n.rateLimiter[url] + 1
	return true
}

//real subject

type application struct {
}

func (a *application) handleRequest(url, method string) (int, string) {
	if url == "/app/status" && method == "GET" {
		return 200, "Ok"
	}

	if url == "/create/user" && method == "POST" {
		return 201, "User Created"
	}
	return 404, "Not Ok"
}

//client code

func main() {

	nginxServer := newNginxServer()
	appStatusURL := "/app/status"
	createuserURL := "/create/user"

	httpCode, body := nginxServer.handleRequest(appStatusURL, "GET")
	fmt.Printf("\nUrl: %s\nHttpCode: %d\nBody: %s\n", appStatusURL, httpCode, body)

	httpCode, body = nginxServer.handleRequest(appStatusURL, "GET")
	fmt.Printf("\nUrl: %s\nHttpCode: %d\nBody: %s\n", appStatusURL, httpCode, body)

	httpCode, body = nginxServer.handleRequest(appStatusURL, "GET")
	fmt.Printf("\nUrl: %s\nHttpCode: %d\nBody: %s\n", appStatusURL, httpCode, body)

	httpCode, body = nginxServer.handleRequest(createuserURL, "POST")
	fmt.Printf("\nUrl: %s\nHttpCode: %d\nBody: %s\n", appStatusURL, httpCode, body)

	httpCode, body = nginxServer.handleRequest(createuserURL, "GET")
	fmt.Printf("\nUrl: %s\nHttpCode: %d\nBody: %s\n", appStatusURL, httpCode, body)
}
```



# Chapter 9. Adapter 

Адаптер — это структурный паттерн, который позволяет подружить несовместимые объекты.

```ruby
class Encrypter 
    def initialize(key)
        @key = key 
    end

    def encrypy(reader, writer)
        key_index = 0
        while not reader.eof? 
            clear_char = reader.getc
            encrypted_char = clear_char ^ @key[key_index]
            writer.putc(encrypted_char)
            key_index = (key_index + 1) % @key.size
        end
    end
end


reader = File.open('message.txt')
writer = File.open('message.encrypted','w')
encrypter = Encrypter.new('my secret key')
encrypter.encrypt(reader, writer)
```

```ruby

class StringIOAdapter 
    def initialize(string)
        @string = string 
        @position = 0
    end
    def getc 
        if @position >= @string.length 
            raise EOFError
        end
        ch = @string[@position]
        @position += 1
        return ch
    end
    def eof? 
        return @position >= @string.length
    end
end

encrypter = Encrypter.new('XYZZY')
reader = StringIOAdapter.new('we attach at dawn')
writer = File.open('out.txt', 'w')
encrypter.encrpyt(reader, writer)
```

## The Near Misses

```ruby
class Renderer
    def render(text_object)
        text = text_object.text 
        size = text_object.size_inches
        color = text_object.color 
    end
end

class TextObject 
    attr_reader :text, :size_inches, :color
    def initialize(text, size_inches, color )
        @text = text 
        @size_inches = size_inches
        @color = color
    end
end

class BritishTextObject
    attr_reader :string, :size_mm, :colour
end

class BritishTextObjectAdapter < TextObject  
    def initialize(bto)
        @bto = bto 
    end

    def text
        return @bto.string
    end

    def size_inches 
        return @bto.size_mm / 25.4
    end

    def color 
        return @bto.colour
    end
end
```


## An adaptive alternative?

```ruby
require 'british_text_object'

class BritishTextObject 
    def color 
        return colour
    end

    def text 
        return string
    end

    def size_inches 
        return size_mm / 25.4
    end
end
```

## Modifying a Single Instance

```ruby 
bto = BritishTextObject.new('hello', 50.4, :blue)

class << bto 
    def color
        colour
    end

    def text 
        string
    end

    def size_inches
        return size_mm / 25.4
    end
end
```

## GOlang Adapter


```golang
package main

import "fmt"


//Клиентский код
type client struct {
}

func (c *client) insertLightningConnectorIntoComputer(com computer) {
	fmt.Println("Client inserts Lightning connector into computer.")
	com.insertIntoLightningPort()
}

//Интерфейс клиента


type computer interface {
	insertIntoLightningPort()
}

//Сервис

type mac struct {
}

func (m *mac) insertIntoLightningPort() {
	fmt.Println("Lightning connector is plugged into mac machine.")
}

//Неизвестный сервис

type windows struct{}

func (w *windows) insertIntoUSBPort() {
	fmt.Println("USB connector is plugged into windows machine.")
}

//Адаптер

type windowsAdapter struct {
	windowMachine *windows
}

func (w *windowsAdapter) insertIntoLightningPort() {
	fmt.Println("Adapter converts Lightning signal to USB.")
	w.windowMachine.insertIntoUSBPort()
}


func main() {

	client := &client{}
	mac := &mac{}

	client.insertLightningConnectorIntoComputer(mac)

	windowsMachine := &windows{}
	windowsMachineAdapter := &windowsAdapter{
		windowMachine: windowsMachine,
	}

	client.insertLightningConnectorIntoComputer(windowsMachineAdapter)
}

```



# Chapter 8. Command pattern 

Команда — это поведенческий паттерн, позволяющий заворачивать запросы или простые операции в отдельные объекты.


```ruby
#wrong way
class SlikButton 
    def on_button_push 
    #
    # Do smth 
    #
    end
end

class SaveButton < SlickButton 
    def on_button_push 
    end
end

class NewDocumentButton < SlickButton
    def on_button_push
    end
end
```

## Easier Way

```ruby
class SlikButton 
    attr_accessor :command 
    def initialize(command)
        @command = command 
    end

    def on_button_push 
        @command.execute if @command
    end
end

class SaveCommand 
    def execute 
        p 'save is processing'
    end
end

btn = SlikButton.new(SaveCommand.new)
btn.on_button_push
```

## Code blocks as Commands

```ruby
class SlikButton 
    attr_accessor :command 
    def initialize(&command)
        @command = command 
    end

    def on_button_push 
        @command.call if @command
    end
end

new_button = SlikButton.new do 
    p 'code block execute'
end
```

## Comands That Record 

```ruby
class Command 
    attr_reader :description 
    def initialize(description)
        @description = description
    end
    def execute
        p 'execute'
    end
end

class CreateFile < Command 
    def initialize(path, contents)
        super('create file: #{path}')
        @path = path 
        @contents = contents
    end
    def execute 
        f = File.open(@path, "w")
        f.write(@contents)
        f.close
    end
end


class DeleteFile < Command
    def initialize(path)
        super("delete #{path}")
        @path = path 
    end
    def execute
        File.delete(@path)
    end
end
```

## Composite Command

```ruby

class CompositeCommand < Command 
    def initialize 
    end

    def add_command(cmd)
        @commands << cmd 
    end

    def execute
        @commands.each {|cmd| cmd.execute}
    end

    def description 
    end
end

cmds = CompositeCommand.new 
cmd.add_command(CreateFile.new)
cmd.add_command(CopyFile.new)
cmd.add_command(DeleteFile.new)
```

## Being Undone by a Command

```ruby
class CreateFile < Command 
    def initialize(path, content)
        super "Create file #{path}"
        @path = path 
        @contents = contents
    end

    def execute
        f = File.open(@path, "w")
        f.write(@contents)
        f.close
    end

    def unexecute
        File.delete(@path)
    end
end

class DeleteFile < Command 
    def initialize(path)
        super "Delete #{path}"
        @path = path 
    end

    def execute 
        if File.exists?(@path)
            @contents = File.read(@path)
        end
        f = File.delete(@path)
    end

    def unexecute 
        if @contents
            f = File.open(@path, "w")
            f.write(@contents)
            f.close
        end
    end
end
```

## Madeleine 

```ruby
require 'rubygems'
require 'madeleine'

class Employee 
    attr_accessor :name, :number, :address 
    def initialize(name, number, address)
        @name = name 
        @number = number
        @address = address 
    end

    def to_s 
        "Employee: #{name} #{number} #{address}"
    end
end

class EmployeeManager
    def initialize
        @employees = {}
    end

    def add_employee(e)
        @employees[e.number] = e 
    end

    def change_address(number, address)
        employee = @employees[number]
        raise 'no such employee' if not employee
        employee.address = address 
    end

    def delete_employee(number)
        @employee.remove(number)
    end

    def find_employee(number)
        @employee[number]
    end
end

class AddEmployee 
    def initialize(employee)
        @employee = employee
    end
    
    def execute(system)
        system.add_employee(@employee)
    end
end

class DeleteEmployee
    def initialize(number)
        @number=number
    end

    def execute(system)
        system.delete_employee(@number)
    end
end

class ChangeAddress 
    def initialize(number, address)
        @number = number 
        @address = address
    end

    def execute(system)
        system.change_address(@number, @addess)
    end
end

class FindEmployee 
    def initialize(number)
        @number = number 
    end

    def execute(system)
        system.find_employee(@number)
    end
end


store = SnapshotMadeleine.new('employees') {EmployeeManager.new}

Thread.new do 
    while true 
        sleep(20)
        madeleine.take_snapshot
    end
end

tom = Employee.new('tom','1001','1 davison')
jerry = Employee.new('jerry','1002','1 123')
store.execute_command(AddEmployee.new(tom))
store.execute_command(AddEmployee.new(jerry))
```


## Golang Command 

```golang
package main

import "fmt"

//отправитель
type button struct {
	command command
}

func (b *button) press() {
	b.command.execute()
}

//Интерфейс команды
type command interface {
	execute()
}


//Кокнретная команда

type onCommand struct {
	device device
}

func (c *onCommand) execute() {
	c.device.on()
}


type offCommand struct {
	device device
}

func (c *offCommand) execute() {
	c.device.off()
}

//Интерфейс получателя

type device interface {
	on()
	off()
}

// Конкретный получатель

type tv struct {
	isRunning bool
}

func (t *tv) on() {
	t.isRunning = true
	fmt.Println("Turning tv on")
}

func (t *tv) off() {
	t.isRunning = false
	fmt.Println("Turning tv off")
}

// Клиентский код

func main() {
	tv := &tv{}

	onCommand := &onCommand{
		device: tv,
	}

	offCommand := &offCommand{
		device: tv,
	}

	onButton := &button{
		command: onCommand,
	}
	onButton.press()

	offButton := &button{
		command: offCommand,
	}
	offButton.press()
}
```

# Chapter 7. Iterator 

Итератор — это поведенческий паттерн проектирования, который даёт возможность последовательно обходить элементы составных объектов, не раскрывая их внутреннего представления.

```ruby
class ArrayIterator 
  def initialize(array)
    @array = array 
    @index = 0
  end

  def has_next? 
    @index < @array.length
  end

  def item
    @array[@index]
  end

  def next_item 
    value = @array[@index]
    @index += 1
    value 
  end
end

array = ['red', 'green', 'blue']

iterator = ArrayIterator.new(array)
while iterator.has_next?
  p "item #{iterator.next_item}"
end
```

## Iterators Golang

```golang
package main

import "fmt"

type collection interface {
	createIterator() iterator
}

//Кокнетная коллекция


type userCollection struct {
	users []*user
}

func (u *userCollection) createIterator() iterator {
	return &userIterator{
		users: u.users,
	}
}

// Итератор

type iterator interface {
	hasNext() bool
	getNext() *user
}

// Конкретный итератор

type userIterator struct {
	index int
	users []*user
}

func (u *userIterator) hasNext() bool {
	if u.index < len(u.users) {
		return true
	}
	return false

}
func (u *userIterator) getNext() *user {
	if u.hasNext() {
		user := u.users[u.index]
		u.index++
		return user
	}
	return nil
}

// Клиентский код
type user struct {
	name string
	age  int
}

func main() {

	user1 := &user{
		name: "a",
		age:  30,
	}
	user2 := &user{
		name: "b",
		age:  20,
	}

	userCollection := &userCollection{
		users: []*user{user1, user2},
	}

	iterator := userCollection.createIterator()

	for iterator.hasNext() {
		user := iterator.getNext()
		fmt.Printf("User is %+v\n", user)
	}
}
```

# Chapter 6. Composite Task 

Компоновщик — это структурный паттерн проектирования, который позволяет сгруппировать множество объектов в древовидную структуру, а затем работать с ней так, как будто это единичный объект.

```ruby
# Abstract base class
class Task 
    attr_reader :name 
    def initialize(name)
        @name = name 
    end

    def get_time_required 
        0.0 
    end
end

class AddIndgredientsTask < Task 
    def initialize 
        super('add dry indigriends')
    end

    def get_time_required
        1.2
    end
end

class MixTask < Task 
    def initialize
        super('mix that batter up')
    end

    def get_time_required
        3.0
    end
end

class MakeBatterTask < Task 
    def initialize
        super('make batter')
        @sub_tasks = []
        add_sub_task( AddIndgredientsTask.new)
        add_sub_task( MixTask.new)
    end

    def add_sub_task(task)
        @sub_tasks << task
    end

    def remove_sub_task(task)
        @sub_tasks.delete(task)
    end

    def get_time_required
        time = 0.0
        @sub_tasks.each { |time| time += task.get_time_required }
        time
    end
end

task = MakeBatterTask.new
```


```ruby
# Abstract base class
class Task 
    attr_reader :name 
    def initialize(name)
        @name = name 
    end

    def get_time_required 
        0.0 
    end
end

class AddIndgredientsTask < Task 
    def initialize 
        super('add dry indigriends')
    end

    def get_time_required
        1.2
    end
end

class MixTask < Task 
    def initialize
        super('mix that batter up')
    end

    def get_time_required
        3.0
    end
end

class CompositeTask < Task 
    def initialize(name)
        super(name)
        @sub_tasks = []
    end

    def <<(task)
        @sub_tasks << task 
    end

    def [](index)
        @sub_tasks[index]
    end

    def []=(index, value)
        @sub_tasks[index] = value 
    end

    def add_sub_task(task)
        @sub_tasks << task
    end

    def remove_sub_task(task)
        @sub_tasks.delete(task)
    end

    def get_time_required
        time = 0.0
        @sub_tasks.each { |task| time += task.get_time_required }
        time
    end
end
 
class MakeBatterTask < CompositeTask 
    def initialize
        super('make batter')
        add_sub_task( AddIndgredientsTask.new )
        add_sub_task( MixTask.new )
    end
end

class MakeCakeTask < CompositeTask 
    def initialize
        super('make cake')
        add_sub_task( MakeBatterTask.new )
    end
end

task = MakeCakeTask.new
task.get_time_required
```

# Chapter 5. Keeping Up with the times with the Observer

Необходимо строить систему, где каждая часть система могла быть в курсе что происходит в других.  

Наблюдатель — это поведенческий паттерн проектирования, который создаёт механизм подписки, позволяющий одним объектам следить и реагировать на события, происходящие в других объектах.

```ruby
class Employee
    attr_reader :name 
    attr_accessor :title, :salary 

    def initialize(name, tilte, salary, payroll) 
        @name = name 
        @title = title 
        @salary = salary
        @payroll = payroll
    end

    def salary=(new_salary)
        @salary = new_salary
        @payroll.update(self)
    end
end


# We need keep Payroll Dep infomed of pay changes 
class Payroll 
    def update( change_employee) 
        p "New check for #{change_employee.name}"
        p "Salary #{change_employee.salary}"
    end
end

payroll = Payroll.new
fred = Employee.new('Fred', 'Crane Operator', 30000, payroll)
fred.salary = 350000
```

## Better way to Stay Informed 

```ruby
class Employee
    attr_reader :name 
    attr_accessor :title, :salary 

    def initialize(name, tilte, salary) 
        @name = name 
        @title = title 
        @salary = salary
        @observers = []
    end

    def salary=(new_salary)
        @salary = new_salary
        notify_observers 
    end

    # Уведомить всех заинтересованных в этом объект наблюдателей
    def notify_observers
        @observers.each do |observer|
            observer.update(self)
        end
    end

    def add_observer(observer)
        @observers << observer 
    end
    
    def delete_observer(observer)
        @observers.delete(observer)
    end
end

class TaxMan 
    def update( changed_employee )
        p "Send #{changed_employee.name} next tax bill"
    end
end

class AccountDepartment 
    def update( changed_employee )
        p "Congratulation #{changed_employee.name}"
    end
end

taxman = TaxMan.new 
account_dep = AccountDepartment.new 
fred = Employee.new('Fred', 'Crane Operator', 30000)
fred.add_observer(taxman)
fred.add_observer(account_dep)
fred.salary = 350000
```

## Factoring Out the Observable Support 

Using module insted inheritance
```ruby
module Subject 
    def initialize
        @observers = [] 
    end

    def add_observer(observer)
        @observers << observer 
    end
    
    def delete_observer(observer)
        @observers.delete(observer)
    end

    # Уведомить всех заинтересованных в этом объект наблюдателей
    def notify_observers
        @observers.each do |observer|
            observer.update(self)
        end
    end

end


class Employee 
    include Subject

    attr_reader :name 
    attr_accessor :title, :salary 

    def initialize(name, tilte, salary) 
        @name = name 
        @title = title 
        @salary = salary
        @observers = []
    end

    def salary=(new_salary)
        @salary = new_salary
        notify_observers 
    end
end

class TaxMan 
    def update( changed_employee )
        p "Send #{changed_employee.name} next tax bill"
    end
end

class AccountDepartment 
    def update( changed_employee )
        p "Congratulation #{changed_employee.name}"
    end
end

taxman = TaxMan.new 
account_dep = AccountDepartment.new 
fred = Employee.new('Fred', 'Crane Operator', 30000)
fred.add_observer(taxman)
fred.add_observer(account_dep)
fred.salary = 350000
```

## Observer ins Standart Ruby Library 

```ruby

require 'observer'


class Employee 
    include Observable

    attr_reader :name 
    attr_accessor :title, :salary 

    def initialize(name, tilte, salary) 
        @name = name 
        @title = title 
        @salary = salary
        @observers = []
    end

    def salary=(new_salary)
        @salary = new_salary
        changed
        notify_observers
    end
end

class TaxMan 
    def update( changed_employee )
        p "Send #{changed_employee.name} next tax bill"
    end
end

class AccountDepartment 
    def update( changed_employee )
        p "Congratulation #{changed_employee.name}"
    end
end

taxman = TaxMan.new 
account_dep = AccountDepartment.new 
fred = Employee.new('Fred', 'Crane Operator', 30000)
fred.add_observer(taxman)
fred.add_observer(account_dep)
fred.salary = 350000
```

## Observer in Wild

Absorever in ruby its ActiveRecord.

```ruby
class EmployeeObserver < ActiveRecord::Observer 
    def after_create(emloyee)
    end

    def after_update(emloyee)
    end

    def after_destroy(emloyee)
    end
end
```

## Observer in Golang 

```golang
package main

import "fmt"

//Издатель
type subject interface {
	register(Observer observer)
	deregister(Observer observer)
	notifyAll()
}

//Конкретный издатель

type item struct {
	observerList []observer
	name         string
	inStock      bool
}

func newItem(name string) *item {
	return &item{
		name: name,
	}
}

func (i *item) changeName(newName string) {
	notify := false
	if (newName != i.name) {
		notify = true
	}
	i.name = newName
	if notify {
		i.updateAvailability()
	}
}

func (i *item) updateAvailability() {
	fmt.Printf("Item %s is now in stock\n", i.name)
	i.inStock = true
	i.notifyAll()
}
func (i *item) register(o observer) {
	i.observerList = append(i.observerList, o)
}

func (i *item) deregister(o observer) {
	i.observerList = removeFromslice(i.observerList, o)
}

func (i *item) notifyAll() {
	for _, observer := range i.observerList {
		observer.update(i.name)
	}
}

func removeFromslice(observerList []observer, observerToRemove observer) []observer {
	observerListLength := len(observerList)
	for i, observer := range observerList {
		if observerToRemove.getID() == observer.getID() {
			observerList[observerListLength-1], observerList[i] = observerList[i], observerList[observerListLength-1]
			return observerList[:observerListLength-1]
		}
	}
	return observerList
}

//Наблюдатель

type observer interface {
	update(string)
	getID() string
}

//Конкретный наблюдатель

type customer struct {
	id string
}

func (c *customer) update(itemName string) {
	fmt.Printf("Sending email to customer %s for item %s\n", c.id, itemName)
}

func (c *customer) getID() string {
	return c.id
}

//Клиентский код

func main() {
	observerFirst := &customer{id: "abc@gmail.com"}
	observerSecond := &customer{id: "xyz@gmail.com"}

	shirtItem := newItem("Nike Shirt")
	shirtItem.register(observerFirst)
	shirtItem.register(observerSecond)
	shirtItem.updateAvailability()

	jacket := newItem("Adidas Jacket")
	jacket.register(observerFirst)
	jacket.register(observerSecond)
	jacket.updateAvailability()
	jacket.changeName("changed name")
}
```



# Chapter 4. Replacing the Algorithm with the Strategy 

Стратегия — это поведенческий паттерн, выносит набор алгоритмов в собственные классы и делает их взаимозаменимыми.

Главный недостатоко шаблонов - он основан на наследовании.  А наследование это игрушка дьявола. 
Если мы хотим создать новый формат, нужно создать целый новый класс. Какая альтернатива?    

## Delegate deleagate and dlegate again

Prefer delegation.  
Мы вывели функиола форматирования шаблонов из Report в отдельный класс. 
Сам Formatter по сути является Template Pattern.   
Его вставка в Report.new() Strategy Pattern  


```ruby
class Formatter
    def output(title, text)
        raise NotImpementedError, "Abstract method #{__method__} called"
    end
end

class HTMLFormatter < Formatter 
    def output(title, text)
        p 'html'
        p title 
        p text
        p 'html'
    end
end

class PlainTextFormatter < Formatter 
    def output(title, text) 
        p title
        p text 
    end
end
# Мы вывели детали вывода из класса Report 

class Report 
    attr_reader :title, :text 
    attr_accessor :formatter 

    def initialize(formatter)
        @title = 'Monthly Report'
        @text = ['Things are going', 'very well toady']
        @formatter = formatter 
    end

    def output
        @formatter.output(@title, @text)
    end
end

Report.new(HTMLFormatter.new).output
Report.new(PlainTextFormatter.new).output

#Можно менять форматироватирнованеи налету
report = Report.new(HTMLFormatter.new)
report.output

report.formatter = PlainTextFormatter.new 
report.output
```

## Sharing Data between the Context and the Strategy

```ruby
class Formatter
    def output(context) # Принять внешний класс как контекст
        raise NotImpementedError, "Abstract method #{__method__} called"
    end
end

class HTMLFormatter < Formatter 
    def output(context)
        p 'html'
        p context.title 
        p context.text
        p 'html'
    end
end

class PlainTextFormatter < Formatter 
    def output(context)  
        p context.title
        p context.text 
    end
end
# Мы вывели детали вывода из класса Report 

class Report 
    attr_reader :title, :text 
    attr_accessor :formatter 

    def initialize(formatter)
        @title = 'Monthly Report'
        @text = ['Things are going', 'very well toady']
        @formatter = formatter 
    end

    def output
        @formatter.output(self) #Послать самого себя как конткет внутрь класса
    end
end

report = Report.new(HTMLFormatter.new)
report.output

report.formatter = PlainTextFormatter.new 
report.output
```

## Duck Typing yet Again 

Мы можем удаить class Formatter 

```ruby
class HTMLFormatter 
    def output(context)
        p 'html'
        p context.title 
        p context.text
        p 'html'
    end
end

class PlainTextFormatter
    def output(context)  
        p context.title
        p context.text 
    end
end
# Мы вывели детали вывода из класса Report 

class Report 
    attr_reader :title, :text 
    attr_accessor :formatter 

    def initialize(formatter)
        @title = 'Monthly Report'
        @text = ['Things are going', 'very well toady']
        @formatter = formatter 
    end

    def output
        @formatter.output(self) #Послать самого себя как конткет внутрь класса
    end
end

report = Report.new(HTMLFormatter.new)
report.output

report.formatter = PlainTextFormatter.new 
report.output
```

## Procs and Blocks 

Самое большое приемущество Proc объектов, то что он берет все окружение(scope) на время создания.   
```ruby
hello = lambda do 
    p 'hello'
    p 'im and inside proc obj'
end
hello.call
```

```ruby
name = 'John'
proc = Proc.new { name = 'Mary'}  
name = 'Travis'
proc.call 
p name 
``` 

```ruby
def run_it
    p 'befroe'
    yield(10) #paste 
    p 'after'
end

run_it do |x|
    p x
    p 'hello'
    p 'coming to you from inside the block'
end
```

```ruby
def run_it(&block) #& - значит что объект уже создан до этого
    p 'befroe'
    block.call(10)
    p 'after'
end

run_it do |x|
    p x
    p 'hello'
    p 'coming to you from inside the block'
end
```

## Quitk and dirty Strategies 

```ruby
HTML_FORMATTER = lambda do |context| 
    p 'html'
    p context.title 
    p context.text
    p 'html'
end

PLAIN_TEXT_FORMATTER = lambda do |context| 
    p context.title 
    p context.text
end

class Report 
    attr_reader :title, :text 
    attr_accessor :formatter 

    def initialize(&formatter)
        @title = 'Monthly Report'
        @text = ['Things are going', 'very well toady']
        @formatter = formatter 
    end

    def output
        @formatter.call(self) #Послать самого себя как конткет внутрь класса
    end
end

report = Report.new(HTML_FORMATTER)
report.output

report.formatter = PLAIN_TEXT_FORMATTER
report.output
```

## Using and abusing strategy pattern 

### rdoc is strategy pattern
rdoc извлекает документацию из кода.  

## GOLANG
```golang
package main

import "fmt"

//Интерфейс стратегии
type evictionAlgo interface {
	evict(c *cache)
}

//Стратегия fifo
type fifo struct {
}

func (l *fifo) evict(c *cache) {
	fmt.Println("Evicting by fifo strtegy")
}

//Стратегия lru
type lru struct {
}

func (l *lru) evict(c *cache) {
	fmt.Println("Evicting by lru strtegy")
}

//Стратегия lfu
type lfu struct {
}

func (l *lfu) evict(c *cache) {
	fmt.Println("Evicting by lfu strtegy")
}

//context

type cache struct {
	storage      map[string]string
	evictionAlgo evictionAlgo
	capacity     int
	maxCapacity  int
}

func initCache(e evictionAlgo) *cache {
	storage := make(map[string]string)
	return &cache{
		storage:      storage,
		evictionAlgo: e,
		capacity:     0,
		maxCapacity:  2,
	}
}

func (c *cache) setEvictionAlgo(e evictionAlgo) {
	c.evictionAlgo = e
}

func (c *cache) add(key, value string) {
	if c.capacity == c.maxCapacity {
		c.evict()
	}
	c.capacity++
	c.storage[key] = value
}

func (c *cache) get(key string) {
	delete(c.storage, key)
}

func (c *cache) evict() {
	c.evictionAlgo.evict(c)
	c.capacity--
}

//client

func main() {
	lfu := &lfu{}
	cache := initCache(lfu)

	cache.add("a", "1")
	cache.add("b", "2")

	cache.add("c", "3")

	lru := &lru{}
	cache.setEvictionAlgo(lru)

	cache.add("d", "4")

	fifo := &fifo{}
	cache.setEvictionAlgo(fifo)

	cache.add("e", "5")
}
```




# Chapter 3. Varying the Algorithm with the Template Method 

Главная идея: 
1. Создать абстратный базовы класс, с скелтом из абстратных методов
2. В подклассах, переписывать абстратные методы, в зависимости от функционала


```ruby
# Примитивный, не гибкий класс, для генерации html
class Report 
    def initialize 
        @title = 'Monthly Report'
        @text = ['Things are going', 'really, really well.' ]
    end

    def output
        p '<html>'
        p @title 
        @text.each do |line| 
            p line
        end
        p '</html>'
    end
end

# Появились новые форматы, класс стал костыльным
class Report 
    def initialize 
        @title = 'Monthly Report'
        @text = ['Things are going', 'really, really well.' ]
    end

    def output(format)
        #Большое кол-во IF
        if format == :plain 
            p @title 
        elsif format == :html
            p '<html>'
            p @title 
            @text.each do |line| 
                p line
            end
            p '</html>'
        else 
            raise "Unknown format: #{format}"
        end
    end
end
```

### Отделить код, который не будет изменятся 

В руби нет настоящийх абстрактных методов и классов.  
Вместо этого мы raise NotImplementedError в абстрактных методах, чтобы невозможно было использовать абстратный класс.

```ruby
class Report 
    def initialize 
        @title = 'Monthly Report'
        @text = ['Things are going', 'really, really well.' ]
    end

    def output_report 
        output_start 
        output_head 
        output_body_start 
        output_body
        output_body_end 
        output_end 
    end

    private 
    def output_body 
        @text.each do |line| 
            output_line(line)
        end
    end

    def output_start 
        raise NotImplementedError, "Called abstract method: #{__method__}"
    end

    def output_head
        raise NotImplementedError, "Called abstract method: #{__method__}"
    end

    def output_body_start
        raise NotImplementedError, "Called abstract method: #{__method__}"
    end

    def output_body_end
        raise NotImplementedError, "Called abstract method: #{__method__}"
    end

    def output_end
        raise NotImplementedError, "Called abstract method: #{__method__}"
    end

end
Report.new.output_report
```

```ruby
class HTMLReport < Report 
    private 

    def output_start 
        p '<html>'
    end

    def output_head 
        p 'head'
    end

    def output_body_start 
        p 'body start'
    end

    def output_line(line)
        p line
    end

    def output_body_end 
        p 'body end'
    end

    def output_end 
        p '</html>'
    end
end
HTMLReport.new.output_report
``` 

```ruby 
# Версия для обычного текста
class PlainTextReport < Report 
    private 

    def output_start 
    end

    def output_head 
    end

    def output_body_start 
    end

    def output_line(line)
        p line
    end

    def output_body_end 
    end

    def output_end 
    end
end
PlainTextReport.new.output_report
```

### Webrick is Template Pattern 

```ruby
require 'webrick'

class HelloServer < WEBrick::GenericServer 
    def run(socket)
        socket.print('hello tcp\ip world')
    end 
end
```

### Template Method GOLANG

```golang
package main

import "fmt"

type iOtp interface {
	genRandomOTP(int) string
	saveOTPCache(string)
	getMessage(string) string
	sendNotification(string) error
	publishMetric()
}

type otp struct {
	iOtp iOtp
}

func (o *otp) genAndSendOTP(otpLength int) error {
	otp := o.iOtp.genRandomOTP(otpLength)
	o.iOtp.saveOTPCache(otp)
	message := o.iOtp.getMessage(otp)
	err := o.iOtp.sendNotification(message)
	if err != nil {
		return err
	}
	o.iOtp.publishMetric()
	return nil
}

type sms struct {
	otp
}

func (s *sms) genRandomOTP(len int) string {
	randomOTP := "1234"
	fmt.Printf("SMS: generating random otp %s\n", randomOTP)
	return randomOTP
}

func (s *sms) saveOTPCache(otp string) {
	fmt.Printf("SMS: saving otp: %s to cache\n", otp)
}

func (s *sms) getMessage(otp string) string {
	return "SMS OTP for login is " + otp
}

func (s *sms) sendNotification(message string) error {
	fmt.Printf("SMS: sending sms: %s\n", message)
	return nil
}

func (s *sms) publishMetric() {
	fmt.Printf("SMS: publishing metrics\n")
}

type email struct {
	otp
}

func (s *email) genRandomOTP(len int) string {
	randomOTP := "1234"
	fmt.Printf("EMAIL: generating random otp %s\n", randomOTP)
	return randomOTP
}

func (s *email) saveOTPCache(otp string) {
	fmt.Printf("EMAIL: saving otp: %s to cache\n", otp)
}

func (s *email) getMessage(otp string) string {
	return "EMAIL OTP for login is " + otp
}

func (s *email) sendNotification(message string) error {
	fmt.Printf("EMAIL: sending email: %s\n", message)
	return nil
}

func (s *email) publishMetric() {
	fmt.Printf("EMAIL: publishing metrics\n")
}

func main() {
	smsOTP := &sms{}
	o := otp{
		iOtp: smsOTP,
	}
	o.genAndSendOTP(4)
	fmt.Println("")
	emailOTP := &email{}
	o = otp{
		iOtp: emailOTP,
	}
	o.genAndSendOTP(4)
}
```


# Chapter 1

## Patterns in Ruby? 

Руби имеет: 
1. DSL
2. Metaprogramming
3. Convetion Not configuration


## Fourteen Out of Twenty Three

В книги будет 14 самых важных паттернов из 23 от Gof 

### Template Method

Код делает всегда одно и тоже, за исключением шага 44. Иногда 44 шаг делает одно, иногда другое.  

### Strategy Object

Не только 44 шаг может быть разнообразным, но и весь алгоритм. Можно обернуть этот алгоритм в Stargey Object

### Observer Pattern

Что если есть класс A, который хочет работать с классом B. Но вы не ходите их соединятб, потому что не знаете, когда может появится класс C и D

### Composite Pattern

Если нужна работа с коллекцией объектов, как с одним объектом.

### Iterator Pattern

Вы хотите спрятать колеекцию объектов. Но не хотите их спрятать слишком глубоко. 
Клиент должен рабоать с объектами этой колекций, и не знать где они хранятся. 

### Command Pattern

Иногда вам нужно обернуть инструкции, как на почтовой открытке. 
"Дорагая датабаза, удали столбе 777, когда получишь это"

### Adapter

Когда вам нужен объект, но его интерфейс неправильный. 

### Proxy

Может у вас есть правильный объект, но он находится где-то в сети. Вам не хочется чтобы клиент забоитлся о его локации. 
Или вам нужно отложить создания объект, как можно на больший срок. 

### Decorator

Инонда вам нужно улучшить объект на лету. 

### Singlton

Вам нужен экземпляр класса, и это должен быть только один экземляр класса в программе. 

### Factory Method

Вы пишите базовый класс, который будет расширен.   
Вы обнаруживаете что вам нужно создать объект, но только подкласс знает какого какого конкретного типа.

### Abstract Factory 

Как вы создатие целое семейство совместимых объектов?  
У вас есть семейство разных автомобилей, но не все двигатели совместимы с системы подачей топлива и воздуха.   
Как решить эту задачу и не создать монструзный код? 

### Builder Pattern 

Вы создаете такой сложный объект, что его конструктор требует большлого количества кода.

### Intrepter 

Может вы чувствуетет что язык программирования неподходит под вашу задачу?? 
Напишите интрепетатор

## You aint' gonna need it

Писать нужно точно нужно прямо сейчас.  Не нужно предсказывать будущее и пытаться написать функционал на перед.  
Не заниматься оверинженирнгом в ущерб ясности кода.   

## Pattern for patterns

1. Seperate out the things that changes from those that stay the same 
2. Program to an interface, not an implementation
3. Prefer composition over intreface


### Пример наследования
Объект отвечает **КАКОГО** он типа.   
Это просто два класса, которые имеют ненужную связть. 
```ruby
class Vehicle 
  def start_engine
    p 'start engine'
  end

  def stop_engine 
    p 'stop enging'
  end
end

# Object KIND OF something 
class Car < Vehicle 
  def sunday_drive 
    start_engine
    stop_engine 
  end
end
```
### Пример композиции
Отвечает на вопрос, какие свойства имеет объект, а не какого он типа.   
Объект машина имеет свойство мотор.  
Мотор бывает дилеьный и бензиновый.   
Ещё тут отделяются изменяемые методы от статических.
```ruby
class Engine 
  def start 
    p 'start'
  end

  def stop 
    p 'stop'
  end
end

class GasolineEngine < Engine 
  def start 
    p 'start gas' 
  end

  def stop 
    p 'stop gas'
  end
end

#object HAS engine, but not kind of engine
class Car 
  def initialize 
    @engine = GasolineEngine.new 
  end

  def sunday_drive 
    @engine.start 
    @engine.stop 
  end
end
```
