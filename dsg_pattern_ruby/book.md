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
