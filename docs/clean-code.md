# 《代码整洁之道》
## 代码猴子与童子军军规
1. 我们就像一群代码猴子，上蹿下跳，自以为领略了编程的真谛。可惜，当我们抓着几个酸桃子，得意洋洋坐到树枝上，却对自己造成的混乱熟视无睹。那堆“可以运行”的乱麻程序，就在我们的眼皮底下慢慢腐坏。

## 第一章 整洁代码
1. 勒布朗法则：稍后等于永不（Later equals never.）
1. 制造混乱无助于赶上期限。混乱只会立刻拖慢你，叫你错过期限。赶上期限的唯一方法——做的快的唯一方法——就是始终尽可能保持代码整洁。
1. Javadoc 中的 `@author` 字段告诉我们自己是什么人。我们是作者。作者都有读者。实际上，作者有责任与读者做良好沟通。下次你写代码的时候，记得自己是作者，要为评判你工作的读者写代码。

## 第二章 有意义的命名
## 第三章 函数
1. 函数的第一规则是要短小。第二条规则是还要更短小...经过漫长的试错，经验告诉我，函数就该小。
1. 函数应该做一件事。做好这件事。只做这一件事。判断函数是否不止做了一件事，有一个方法，就是看是否能再拆出一个函数，该函数不仅只是单纯地重新诠释其实现。
1. 要确保函数只做一件事，函数中的语句都要在同一抽象层级上。函数中混杂不同的抽象层级，往往让人迷惑。读者可能无法判断某个表达式是基础概念还是细节。更恶劣的是，就像破损的窗户，一旦细节与基础概念混杂，更多的细节就会在函数中纠结起来。
1. 写出短小的 switch 语句往往很难。写出只做一件事的 switch 语句也很难。我们总不发避开 switch 语句，不过还是能够确保 switch 都埋藏在较低的抽象层级，而且永远不重复。
1. 最理想的参数数量是零，其次是一，再次是二...从测试的角度看，参数甚至更叫人为难。想想看，要编写能确保参数的各种组合运行正常的测试用例，是多么困难的事。如果没有参数，就是小菜一碟。
1. 函数承诺只做一件事，但还是会做其它被藏起来的事。有时，它会对自己类中的变量做出未能预期的改动，导致古怪的时序性耦合及顺序依赖。

```java
public class UserValidator {
    private Cryptographer cryptographer;

    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != null) {
            String codePhrase = user.getPhraseEncodeByPassword();
            String phrase = cryptographer.decrypt(codePhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```

副作用就在于对 `Session.initialize()` 的调用。`checkPassword` 函数是用来检查密码的。该名称未暗示它会初始化该次会话。当某个误信了函数名的调用者想要检查用户有效性时，就得冒着抹除现有会话数据的风险。这一副作用造成了一次时序性耦合。也就是说，`checkPassword` 只能在特定时刻调用。

## 第四章 注释
1. 注释的恰当用法是弥补我们在用代码表达意图时遭遇的失败。注释总是一种失败。我们总无法找到不用注释就能表达自我的方法，所以总要有注释，这并不值得庆贺。
2. 如果你发现自己需要写注释，再想想看是否有办法翻盘，用代码来表达。
1. 有时，有理由用 `// TODO` 形式在源代码中放置要做的工作列表。`TODO` 是一种程序员认为应该做，但由于某些原因目前还没做的工作。
1. 没有什么比被良好描述的公共 API 更有用和令人满意的了。如果你在编写公共 API，就该为它编写良好的 Javadoc。
1. 删掉无用而多余的 Javadoc 吧，这些注释只是一味将代码搞得含糊不明，完全没有文档上的价值。
1. 所谓每个函数都要有 Javadoc 或每个变量都要有注释的规矩全然是愚蠢可笑的。这类注释徒然让代码变得散乱，满口胡言，令人迷惑不解。
1. 20 世纪 60 年代，曾经有那么一段时间，注释掉的代码可能有用。但我们已经拥有优良的源代码控制系统如此之久，这些系统可以为我们记住不要的代码。我们无需再用注释来标记，删掉即可，它们丢不了，我担保。

## 第五章 格式
1. 代码格式很重要，必须严肃对待。代码格式关乎沟通，而沟通是专业开发者的头等大事。
1. 你今天编写的功能，极有可能在下一版本中被修改，但代码的可读性却会对以后可能发生的修改行为产生深远影响。原始代码修改之后很久，其代码风格和可读性仍会影响到可维护性和扩展性。即便代码不复存在，你的风格和律条仍会存活下来。
1. 若某个函数调用了另外一个，就应该把它们放在一起，而且调用者应该尽可能放在被调用者上面。

## 第六章 对象和数据结构
1. 最为精炼的数据结构，是一个只有公共变量、没有函数的类。这种数据结构有时被称为数据传送对象，或 DTO（Data Transfer Objects）。DTO 是非常有用的结构，尤其是在于数据库通信、或解析套接字传输的消息之类的场景中。

## 第七章 使用异常而非返回码
1. 在很久以前，许多语言都不支持异常。这些语言处理和汇报错误的手段都有限。你要么设置一个错误标识，要么返回给调用者检查的错误码。这类手段的问题在于，它们搞乱了调用者代码。调用者必须在调用之后即可检查错误。不幸的是，这个步骤很容易被遗忘。最好是抛出一个异常，这样其逻辑不会被错误处理搞乱。
1. 使用不可控异常。可控异常 `checked exception` 的代价是违反开闭原则。如果你在方法中抛出可控异常，而 catch 语句在三个层级之上，你就得在 catch 语句和抛出异常处之间的每个方法签名中声明该异常。这意味着对软件中低层级的修改，都将涉及较高层级的签名。最终得到的就是一个从软件最底端贯穿到最高端的修改链。
1. 别返回 null 值。我不想去计算曾经见过多少每行代码都在检查 null 值的应用程序。Java 中有 `Colletions.emptyList()` 方法，该方法返回一个预定义不可变列表，这样编码，就能尽量避免 `NullPointerException` 的出现，代码也就更整洁了。
1. 别传递 null 值。在大多数编程语言中，没有良好的方法能对付由调用者意外传入 null 值。事已如此，恰当的做法就是禁止传入 null 值。