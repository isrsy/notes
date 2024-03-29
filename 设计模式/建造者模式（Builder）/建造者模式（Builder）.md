# 建造者模式（Builder）

## 目录

*   [模式动机](#模式动机)

*   [模式定义](#模式定义)

*   [模式结构](#模式结构)

    *   [时序图](#时序图)

*   [代码分析](#代码分析)

    *   *   [程序入口](#程序入口)

*   [模式分析](#模式分析)

*   [优点](#优点)

*   [缺点](#缺点)

*   [适用环境](#适用环境)

*   [模式扩展](#模式扩展)

*   [总结](#总结)

# 模式动机

复杂对象相当于一辆有待建造的汽车，而对象的属性相当于汽车的部件，建造产品的过程就相当于组合部件的过程。由于组合部件的过程很复杂，因此，这些部件的组合过程往往被“外部化”到一个称作建造者的对象里，建造者返还给客户端的是一个已经建造完毕的完整产品对象，而用户无须关心该对象所包含的属性以及它们的组装方式，这就是建造者模式的模式动机。

# 模式定义

造者模式(Builder Pattern)：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。建造者模式属于对象创建型模式。根据中文翻译的不同，建造者模式又可以称为生成器模式。

# 模式结构

建造者模式包含如下角色：

*   Builder：抽象建造者

*   ConcreteBuilder：具体建造者

*   Director：指挥者

*   Product：产品角色

![](image/image_pQCzpjmCqL.png)

## 时序图

![](image/image_3wuGAL20zh.png)

# 代码分析

创建一个英雄对象

```java
public final class Hero {

  private final Profession profession;
  private final String name;
  private final HairType hairType;
  private final HairColor hairColor;
  private final Armor armor;
  private final Weapon weapon;

  private Hero(Builder builder) {
    this.profession = builder.profession;
    this.name = builder.name;
    this.hairColor = builder.hairColor;
    this.hairType = builder.hairType;
    this.weapon = builder.weapon;
    this.armor = builder.armor;
  }
 
  // 省略get set方法

  public static class Builder {

    private final Profession profession;
    private final String name;
    private HairType hairType;
    private HairColor hairColor;
    private Armor armor;
    private Weapon weapon;

    /**
     * Constructor.
     */
    public Builder(Profession profession, String name) {
      if (profession == null || name == null) {
        throw new IllegalArgumentException("profession and name can not be null");
      }
      this.profession = profession;
      this.name = name;
    }

    public Builder withHairType(HairType hairType) {
      this.hairType = hairType;
      return this;
    }

    public Builder withHairColor(HairColor hairColor) {
      this.hairColor = hairColor;
      return this;
    }

    public Builder withArmor(Armor armor) {
      this.armor = armor;
      return this;
    }

    public Builder withWeapon(Weapon weapon) {
      this.weapon = weapon;
      return this;
    }

    public Hero build() {
      return new Hero(this);
    }
  }
}
```

职业

```java
public enum Profession {

  WARRIOR, THIEF, MAGE, PRIEST;

  @Override
  public String toString() {
    return name().toLowerCase();
  }
}
```

头发

```java
@AllArgsConstructor
public enum HairType {
  BALD("bald"),
  SHORT("short"),
  CURLY("curly"),
  LONG_STRAIGHT("long straight"),
  LONG_CURLY("long curly");

  private final String title;

  @Override
  public String toString() {
    return title;
  }
}

public enum HairColor {
  WHITE,
  BLOND,
  RED,
  BROWN,
  BLACK;

  @Override
  public String toString() {
    return name().toLowerCase();
  }
}
```

盔甲

```java
@AllArgsConstructor
public enum Armor {

  CLOTHES("clothes"),
  LEATHER("leather"),
  CHAIN_MAIL("chain mail"),
  PLATE_MAIL("plate mail");

  private final String title;

  @Override
  public String toString() {
    return title;
  }
}

```

武器

```java
public enum Weapon {
  DAGGER, SWORD, AXE, WARHAMMER, BOW;
  @Override
  public String toString() {
    return name().toLowerCase();
  }
}
```

### 程序入口

```java
var mage = new Hero.Builder(Profession.MAGE, "Riobard")
    .withHairColor(HairColor.BLACK)
    .withWeapon(Weapon.DAGGER)
    .build();
LOGGER.info(mage.toString());

var warrior = new Hero.Builder(Profession.WARRIOR, "Amberjill")
    .withHairColor(HairColor.BLOND)
    .withHairType(HairType.LONG_CURLY).withArmor(Armor.CHAIN_MAIL).withWeapon(Weapon.SWORD)
    .build();
LOGGER.info(warrior.toString());

var thief = new Hero.Builder(Profession.THIEF, "Desmond")
    .withHairType(HairType.BALD)
    .withWeapon(Weapon.BOW)
    .build();
LOGGER.info(thief.toString());
```

# 模式分析

抽象建造者类中定义了产品的创建方法和返回方法;

建造者模式的结构中还引入了一个指挥者类Director，该类的作用主要有两个：一方面它隔离了客户与生产过程；另一方面它负责控制产品的生成过程。指挥者针对抽象建造者编程，客户端只需要知道具体建造者的类型，即可通过指挥者类调用建造者的相关方法，返回一个完整的产品对象

在客户端代码中，无须关心产品对象的具体组装过程，只需确定具体建造者的类型即可，建造者模式将复杂对象的构建与对象的表现分离开来，这样使得同样的构建过程可以创建出不同的表现。

# 优点

*   在建造者模式中， **客户端不必知道产品内部组成的细节，将产品本身与产品的创建过程解耦，使得相同的创建过程可以创建不同的产品对象。**

*   每一个具体建造者都相对独立，而与其他的具体建造者无关，因此可以很方便地替换具体建造者或增加新的具体建造者， **用户使用不同的具体建造者即可得到不同的产品对象** 。

*   **可以更加精细地控制产品的创建过程** 。将复杂产品的创建步骤分解在不同的方法中，使得创建过程更加清晰，也更方便使用程序来控制创建过程。

*   **增加新的具体建造者无须修改原有类库的代码，指挥者类针对抽象建造者类编程，系统扩展方便，符合“开闭原则”。**

# 缺点

*   建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，如果产品之间的差异性很大，则不适合使用建造者模式，因此其使用范围受到一定的限制。

*   如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大。

# 适用环境

在以下情况下可以使用建造者模式：

*   需要生成的产品对象有复杂的内部结构，这些产品对象通常包含多个成员属性。

*   需要生成的产品对象的属性相互依赖，需要指定其生成顺序。

*   对象的创建过程独立于创建该对象的类。在建造者模式中引入了指挥者类，将创建过程封装在指挥者类中，而不在建造者类中。

*   隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同的产品。

模式应用

在很多游戏软件中，地图包括天空、地面、背景等组成部分，人物角色包括人体、服装、装备等组成部分，可以使用建造者模式对其进行设计，通过不同的具体建造者创建不同类型的地图或人物。

1.  java.lang.StringBuilder

2.  java.nio.ByteBuffer as well as similar buffers such as FloatBuffer, IntBuffer and so on.

3.  java.lang.StringBuffer

4.  All implementations of java.lang.Appendable

5.  Apache Camel builders

6.  Apache Commons Option.Builder

# 模式扩展

建造者模式的简化:

*   省略抽象建造者角色：如果系统中只需要一个具体建造者的话，可以省略掉抽象建造者。

*   省略指挥者角色：在具体建造者只有一个的情况下，如果抽象建造者角色已经被省略掉，那么还可以省略指挥者角色，让

Builder角色扮演指挥者与建造者双重角色。

建造者模式与抽象工厂模式的比较:

*   与抽象工厂模式相比， **建造者模式返回一个组装好的完整产品** ，而 **抽象工厂模式返回一系列相关的产品，这些产品位于不同的产品等级结构，构成了一个产品族。**

*   在抽象工厂模式中，客户端实例化工厂类，然后调用工厂方法获取所需产品对象，而在建造者模式中，客户端可以不直接调用建造者的相关方法，而是通过指挥者类来指导如何生成对象，包括对象的组装过程和建造步骤，它侧重于一步步构造一个复杂对象，返回一个完整的对象。

*   如果将抽象工厂模式看成 **汽车配件生产工厂** ，生产一个产品族的产品，那么建造者模式就是一个 **汽车组装工厂** ，通过对部件的组装可以返回一辆完整的汽车。

# 总结

*   建造者模式将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。建造者模式属于对象创建型模式。

*   建造者模式包含如下四个角色：抽象建造者为创建一个产品对象的各个部件指定抽象接口；具体建造者实现了抽象建造者接口，实现各个部件的构造和装配方法，定义并明确它所创建的复杂对象，也可以提供一个方法返回创建好的复杂产品对象；产品角色是被构建的复杂对象，包含多个组成部件；指挥者负责安排复杂对象的建造次序，指挥者与抽象建造者之间存在关联关系，可以在其construct()建造方法中调用建造者对象的部件构造与装配方法，完成复杂对象的建造

*   在建造者模式的结构中引入了一个指挥者类，该类的作用主要有两个：一方面它隔离了客户与生产过程；另一方面它负责控制产品的生成过程。指挥者针对抽象建造者编程，客户端只需要知道具体建造者的类型，即可通过指挥者类调用建造者的相关方法，返回一个完整的产品对象。

*   建造者模式的主要优点在于客户端不必知道产品内部组成的细节，将产品本身与产品的创建过程解耦，使得相同的创建过程可以创建不同的产品对象，每一个具体建造者都相对独立，而与其他的具体建造者无关，因此可以很方便地替换具体建造者或增加新的具体建造者，符合“开闭原则”，还可以更加精细地控制产品的创建过程；其主要缺点在于由于建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，因此其使用范围受到一定的限制，如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大。

*   建造者模式适用情况包括：需要生成的产品对象有复杂的内部结构，这些产品对象通常包含多个成员属性；需要生成的产品对象的属性相互依赖，需要指定其生成顺序；对象的创建过程独立于创建该对象的类；隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同类型的产品。
