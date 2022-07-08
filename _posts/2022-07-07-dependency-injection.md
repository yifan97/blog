---
layout: post
current: post
cover: assets/images/cover-img/c26.jpg
navigation: True
title: Dependency Injection
date: 2022-07-07
comments: true
---

This post gives a simple explanation of dependency injection.

---

### **What is dependency injection**

Before I give the definition, let's look at an example. 

```java
{
    public interface Animal {
        String makeSound();
    }

    public final class Bird implements Animal {
        @Override
        public String makeSound() {
            return "tweet";
        }
    }
}
```

```java
{
    class Zoo {
        private Animal animal;

        public Zoo(){
            animal = new Bird();
        }

        public Animal getAnimal(){
            return animal;
        }
    }
}
```

This is an example of not using dependency injection. You can see that the animal bird is instantiated in the constructor of zoo object and it works just fine now. What if, in the future, more monkeys, tigers, ostriches, etc, are introduced to the zoo? In that case, **animal** variable can't be used to refer a single animal and you would instead need to modify it to 

```java
    bird = new Bird();
    monkey = new Monkey();
    ...

```

This is when dependency injection comes into play. 

```java
{
    class Zoo {
        private Animal animal;

        public Zoo(Animal animal){
            this.animal = animal
        }

        public Animal getAnimal(){
            return animal;
        }
    }
}
```

```java
{
    Zoo zoo = new Zoo(new Bird());
    System.out.println(zoo.getAnimal().makeSound());
}
```

That's it. Now you know dependency injection, generally, is injecting your dependencies into the class, instead of the class instantiating its own dependencies. In java, this is simply done by passing dependencies into a class's constructor. 

### **Why should we use injection dependency**

- Decoupling. As shown in above example, Zoo class depends on Animal interface instead of Bird implementation, so the classes are decoupled. 
- Ease of test. In unit test, with dependency injection, we can pass a mocked "Animal" to the Zoo to simplify tests.
- Maintainability. Even when more animals are introduced, we can alwasy construct the zoo with more dependencies <mark> Zoo zoo = new Zoo(new Bird(), new Monkey(), new Tiger());</mark>
