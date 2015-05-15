---
layout: post
title:  "thinkphp中的关联表"
date:   2015-05-12 22:06:05
categories: Front-end
excerpt: thinkphp。
---

* content
{:toc}



## thinkphp中的关联表

**任务：** [HTML、CSS基础](https://github.com/Gaohaoyang/ife/tree/master/task/task0001)   

在复杂的关联操作中，如果要给关联定义增加可选的属性，我们可以采用完整定义的方式。
完整定义的格式是：

protected $_link = array(

    '关联表名1'  =>  array(
        '该表的关联方式的属性1' => '定义',
        '该表的关联方式的属性N' => '定义',
    ),
    '关联表名2'  =>  array(
        '该表的关联方式的属性1' => '定义',
        '该表的关联方式的属性N' => '定义',
    ),
    ...
);   

---

在上面的格式描述中，对于属性我们使用了一个修饰词：该表的关联方式的属性。

关联操作的定义之所以觉得复杂和容易出错，也许是因为可定义的属性比较多，而且因为各种关联关系不同，可定义的属性也有部分区别，其实只要理解透这个知识点，你会发现关联操作其实也很简单。

我们换一种方式去啃下这个最麻烦的难点，先来了解所有允许定义的属性含义，然后再按照各种关联关系去对号入座。
完整方式的定义，替换上面的格式，会类似这样，这里以Area表进行示例说明允许定义的属性这个概念

class UserModel extends Model{  

    protected $_link = array(
        'Area'=>array(
             'mapping_type'=> BELONGS_TO,
             'mapping_name'=> 'Area',
             'class_name' => 'Area',
             'foreign_key' => 'area_id',
        ),
    );}

上面代码中，mapping_type,foreign_key ...这些，就是允许定义的属性 

-------------------------所有可定义的属性概述--------------------------------  
### mapping_type   

用以定义该表与主表的关联类型mapping_type所定义的内容必须是常量，共有下面四种方式： 

HAS_ONE  表示当前模型(主表)拥有一个子对象，例如本例中的   
  
user_info，每个会员对应一个资料表  

Belongs_to  表示当前模型从属于另外一个父对象，例如每个会员都从属于一个地区  

HAS_MANY  表示当前模型拥有多个子对象，例如每个用户有多个兴趣消费卡  

MANY_TO_MANY 表示当前模型可以属于多个对象，而父对象则可能包含有多个子对象，通常两者之间需要一个中间表类约束和关联。例如本例中的每个会员可以属于多个兴趣组，每个兴趣组可以有多个用户  

###class_name  

要关联的模型类名称。
例如示例代码所定义的Area，再次提醒，一定要确保已定义了此模型类(AreaModel)。  

###mapping_name
关联的映射名称，用于获取数据用，作为数据集中与主表字段并列的数据索引。  

该名称不能与当前模型的字段重复，否则会导致关联数据获取的冲突mapping_name没有定义的话，会取class_name的定义作为mapping_name。即模型类名称
###foreign_key
关联的外键名称  

foreign_key如果没定义，则默认是 当前数据对象名称+_id 作为外键
例如：UserModel对应的表是think_user(注意：think只是一个数据表前缀，可以随意配置)
那么think_user表的外键默认为user_id，如果不是，就用该属性进行显式指定，例如数据表定义的外键是userId,则使用'foreign_key'=>'userId',这种方式去申明
###Condition
关联条件，在关联查询的时候会自动带上外键的值，如果有额外的查询条件，可以使用该属性进行定义  

###mapping_fields
关联要查询的字段
如果没有定义，默认关联查询的关联数据是该关联表的全部字段。
如果仅仅是需要查询其中的个别字段，可以使用该属性进行指定
###as_fields
直接把关联的字段值映射成数据对象中的某个字段
这个特性是ONE_TO_ONE(包括HAS_ONE 和 BELONGS_TO)关联关系特有的，可以直接把关联数据映射到数据对象中，而不是作为一个关联数据。例如当关联数据的字段名和当前数据对象的字段名称有冲突时，可以使用下面这种方式进行定义：

'as_fields'=>'email,nickname:username',没有定义该属性时数据类似于： 

["id"] => string(1) "2" 

["name"] => string(8) "thinkphp" 

["Card"] => array(1) {
    [0] => array(1) {
        ["card"] => string(8) "88888888"
    } 

如果定义了映射，则类似于
["id"]   => string(1) "2"
["name"] => string(8) "thinkphp"
["card"] => string(8) "88888888"
###parent_key
自引用关联的关联字段
默认为parent_id (BELONGS_TO和HAS_MANY才有此属性)
自引用关联是一种比较特殊的关联，也就是关联表就是当前表。
###mapping_limit
关联要返回的记录数目(HAS_MANY和MANY_TO_MANY才有此属性)
###mapping_order
关联查询的排序 (HAS_MANY和MANY_TO_MANY才有此属性)
###relation_foreign_key
关联表的外键名称 (MANY_TO_MANY独有属性)
默认的关联表的外键名称是 表名+_id
例如本示例中的relation_foreign_key是group_id
如果不符合，可通过此属性进行指定
###relation_table
关联的中间表名称 (MANY_TO_MANY独有属性)

如果没有设置该属性，默认通过当前模型的getRelationTableName方法来自动获取

例如当前模型是User，关联模型是Group，那么关联表的名称会默认使用user_group这样的格式。

默认的中间表命名规则是：

如果两个表存在一个对应的中间表，该中间表的命名方式为

数据表前缀+_ +关联操作的主表名+_+关联表名

例如本示例中，由think_user表操作关联表,所以和think_group表的中间表名称是think_user_group，如果是从think_group表来操作，那么中间命名是think_group_user，也就是说，多对多关联的设置，必须有一个Model类里面需要显式定义中间表，否则双向操作会出错。
另外，中间表的字段无需要有id主键，(不过就算建立了这个字段也不会影响中间表操作的)中间表通常只是由 user_id 和 group_id 构成就可以了。

如果中间表不符合上面的规则，则需要通过该属性去显式指定中间表名称。

各种关联关系类型可用属性归纳如下，使用中可对照此表和参考上面定义解释

 HAS_ONE 可用属性 

mapping_type

class_name

mapping_name

foreign_key

condition

mapping_fields

as_fields

BELONGS_TO 可用属性

mapping_name

foreign_key

mapping_fields

condition

parent_key

as_fields

HAS_MANY 可用属性

mapping_name

foreign_key

parent_key

mapping_fields

mapping_limit

mapping_order

MANY_TO_MANY 可用属性

mapping_name

foreign_key

elation_foreign_key

mapping_limit

mapping_order

relation_table

关联定义好后，我们就可以轻松使用关联操作了。
以下示例代码来自官方总工程师的关联操作示例，先贴出所有代码再对重点要注意的地方进行讲解。
 
<?php

class IndexAction extends Action{

    public function index(){
        $User = D("User");

-------------- 关联写入 -------------------------------

        // 添加用户数据
        $User->name =  'thinkphp';
        $User->area_id   =  1;//东区
        // 用户档案数据
        $User->UserInfo =  array(
            'email'  =>'liu21st@gmail.com',
         'nickname' =>'流年',
        );
        // 用户的消费卡数据
        $User->Card   =  array(
            array('id'=>1,'card'=>'12345678'),// 消费卡帐号1
            array('id'=>2,'card'=>'88888888'),// 消费卡帐号2
            );
        // 用户的所属兴趣组数据
        $User->Group =  array(
            array('id'=>1),// 琴
            array('id'=>2),// 棋
        );
        // 关联添加用户数据
        $id = $User->relation(true)->add();
        // 如果用户数据不是User模型 而是一个Data数组
        // 可以使用
        // $id = $User->relation(true)->add($Data);
        $this->assign('info1', '用户数据关联写入完成！');

------------- 关联查询 -------------------------------

        $user   =  $User->relation(true)->find($id);
        $this->assign('user1',$user);
        $user   =  $User->relation('UserInfo')->find($id);
        $this->assign('user2',$user);
        $list   =  $User->relation(true)->findAll();
        $this->assign('list',$list);

-------------- 关联更新 -------------------------------

        $user['id']   = $id;
        $user['name'] = 'tp';
        // HAS_ONE 关联数据的更新直接赋值
        $user['UserInfo']['email'] =  'thinkphp@qq.com';
        // 注意HAS_MANY 的关联数据要加上主键的值
        // 可以更新部分数据
        $user['Card'] = array(
            array('id'=>1,'card'=>'66666666'), // 更新主键为1的记录
            array('card'=>'77777777'),// 增加一条新的记录
        );
        // MANY_TO_MANY 的数据更新是重新写入
        $user['Group'] =  array(
            array('id'=>2),
        );
        $User->where('id='.$id)->relation(true)->save($user);
        // 查询更新后的数据
        $user   =  $User->relation(true)->find($id);
        $this->assign('user3',$user);

-------------- 关联删除 -------------------------------

        $User->relation(true)->delete($id);
        $this->assign('info2', '用户ID为'.$id.'的数据已经关联删除！');
        $this->assign('id',$id);
        $this->display();
    }
}
?>
代码注解：
TP关联操作支持手动关联和自动关联，我们看到上面代码使用的是手动关联，通过在连贯操作中的relation()方法进行控制，如果需要自动关联操作，可以在UserModel中设置相应属性。
 
class UserModel extends RelationModel{

    protected $autoSaveRelations = true;   // 自动关联保存
    protected $autoDelRelations  = true;   // 自动关联删除
    protected $autoAddRelations  = true;   // 自动关联写入
    protected $autoReadRelations = true;    // 自动关联查询
    protected $_link = array(....);
}

例如当设置了自动关联查询属性为true时，使用$User->select()，系统会自动把所有定义的关联数据都查询出来，自动关联的应用可以自己进行测试体验，官方的建议是：当给模型定义了多个关联的时候，要尽量避免使用自动关联操作，以免影响性能。

再来看上面的代码，插入数据时我们使用了这种方式：

// 用户档案数据
$User->UserInfo =  array(
    'email'  =>'liu21st@gmail.com',
    'nickname' =>'流年',
);

是因为我们以User模型为核心对其它模型类进行关联定义的，其它模型类的mapping_name属性所定义的值，会被映射到User数据对象中成为User数据对象的其中一个属性，黙认mapping_name属性是关联的模型类名称。

如果是ON\_TO\_ONE关联关系，并且定义了as_fields字段映射，则as_fields所设置的字段，会直接作为user数据对象的字段，这种情况在数据处理方式上，和单表模型是相同的，即直接赋值就可以：
$User->email    = 'liu21st@gmail.com';

$User->nickname = '流年';
relation()方法在ThinkPHP1.6版中进行了改进，简化了以前的几种我们所熟悉的 xFind(),xFindAll(),relationGet(),relationAdd(),relationSave(),relationDel()这些方法，全部整合到了新定义的relation()中，直接支持使用参数进行连贯操作。

当需要进行关联处理时，我们使用relation(ture)这种方式:

$id = $User->relation(true)->add();
如果要指定关联数据，可以如34行代码中那样，在relation方法中传入参数：

$user =$User->relation('UserInfo')->find($id);

至于关联更新的需要注意的是HAS_ONE 关联数据的更新直接赋值则可，
$user['UserInfo']['email'] =  'thinkphp@qq.com';
如果是HAS_MANY 的关联数据则要加上主键的值，可以更新部分数据或添加新的数据

$user['Card'] = array(

    array('id'=>1,'card'=>'66666666'), // 更新主键为1的记录
    array('card'=>'77777777'),// 增加一条新的记录
);

MANY_TO_MANY关联在插入和更新时，都会自动先将原来关联的数据删除再进行处理。
打开Runtime/Logs/的log日志或开启debug模式时，可以留意一下生成的sql。

---




---







