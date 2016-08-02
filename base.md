#基础常识
##1 equals,==
equals比较的是内容，==比较的是内存地址。Object类的equals方法比较的是地址
###为什么重写equals时要重写hashCode?
	如果你重载了equals，比如说是基于对象的内容实现的，而保留hashCode的实现不变，那么很可能某
	两个对象明明是“相等”，而hashCode却不一样。这样，当你用其中的一个作为键保存到hashMap、
	hasoTable或hashSet中，再以“相等的”找另一个作为键值去查找他们的时候，则根本找不到。使用
	HashMap，如果key是自定义的类，就必须重写hashcode()和equals()。而对于每一个对象，通过
	其hashCode()方法可为其生成一个整形值（散列码），该整型值被处理后，将会作为数组下标，存放
	该对象所对应的Entry（存放该对象及其对应值）。 equals()方法则是在HashMap中插入值或查
	询时会使用到。当HashMap中插入值或查询值对应的散列码与数组中的散列码相等时，则会通
	过equals方法比较key值是否相等，所以想以自建对象作为HashMap的key，必须重写该对象继
	承object的hashCode和equals方法。 2.本来不就有hashcode()和equals()了么？干嘛要
	重写，直接用原来的不行么？ HashMap中，如果要比较key是否相等，要同时使用这两个函数！因
	为自定义的类的hashcode()方法继承于Object类，其hashcode码为默认的内存地址，这样即便
	有相同含义的两个对象，比较也是不相等的，例如，生成了两个“羊”对象，正常理解这两个对象应该
	是相等的，但如果你不重写 hashcode（）方法的话，比较是不相等的！HashMap中的比较key是这
	样的，先求出key的hashcode(),比较其值是否相等，若相等再比较equals(),若相等则认为他们
	是相等的。若equals()不相等则认为他们不相等。如果只重写hashcode()不重写equals()方法
	，当比较equals()时只是看他们是否为同一对象（即进行内存地址的比较）,所以必定要两个方法
	一起重写。HashMap用来判断key是否相等的方法，其实是调用了HashSet判断加入元素是否相等。
	

