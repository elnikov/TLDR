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
