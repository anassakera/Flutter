

1. **Container**
```dart
Container(
  width: 100,
  height: 100,
  color: Colors.blue,
);
```

2. **Column**
```dart
Column(
  children: [
    Text('Item 1'),
    Text('Item 2'),
  ],
);
```

3. **Row**
```dart
Row(
  children: [
    Text('Item 1'),
    Text('Item 2'),
  ],
);
```

4. **Stack**
```dart
Stack(
  children: [
    Container(color: Colors.blue, width: 100, height: 100),
    Positioned(
      left: 10,
      top: 10,
      child: Container(color: Colors.red, width: 50, height: 50),
    ),
  ],
);
```

5. **ListView**
```dart
ListView(
  children: [
    ListTile(title: Text('Item 1')),
    ListTile(title: Text('Item 2')),
  ],
);
```

6. **GridView**
```dart
GridView.count(
  crossAxisCount: 2,
  children: [
    Container(color: Colors.blue, height: 100),
    Container(color: Colors.red, height: 100),
  ],
);
```

7. **SingleChildScrollView**
```dart
SingleChildScrollView(
  child: Column(
    children: List.generate(20, (index) => Text('Item $index')),
  ),
);
```

8. **SizedBox**
```dart
SizedBox(
  width: 100,
  height: 100,
  child: Container(color: Colors.blue),
);
```

9. **Padding**
```dart
Padding(
  padding: EdgeInsets.all(16.0),
  child: Text('Padded Text'),
);
```

10. **Center**
```dart
Center(
  child: Text('Centered Text'),
);
```

11. **Align**
```dart
Align(
  alignment: Alignment.topRight,
  child: Text('Aligned Text'),
);
```

12. **Positioned**
```dart
Stack(
  children: [
    Container(color: Colors.blue, width: 100, height: 100),
    Positioned(
      left: 10,
      top: 10,
      child: Container(color: Colors.red, width: 50, height: 50),
    ),
  ],
);
```

13. **Expanded**
```dart
Row(
  children: [
    Expanded(child: Container(color: Colors.blue)),
    Container(width: 100, color: Colors.red),
  ],
);
```

14. **Flexible**
```dart
Row(
  children: [
    Flexible(child: Container(color: Colors.blue)),
    Container(width: 100, color: Colors.red),
  ],
);
```

15. **Spacer**
```dart
Row(
  children: [
    Text('Start'),
    Spacer(),
    Text('End'),
  ],
);
```

16. **Text**
```dart
Text('Simple Text'),
```

17. **RichText**
```dart
RichText(
  text: TextSpan(
    text: 'Hello ',
    style: DefaultTextStyle.of(context).style,
    children: <TextSpan>[
      TextSpan(text: 'bold', style: TextStyle(fontWeight: FontWeight.bold)),
      TextSpan(text: ' world!'),
    ],
  ),
);
```

18. **Icon**
```dart
Icon(Icons.star),
```

19. **Image**
```dart
Image.network('https://flutter.dev/images/flutter-logo-sharing.png'),
```

20. **CircleAvatar**
```dart
CircleAvatar(
  backgroundImage: NetworkImage('https://example.com/image.jpg'),
);
```

21. **Chip**
```dart
Chip(
  label: Text('Chip'),
);
```

22. **Card**
```dart
Card(
  child: Padding(
    padding: EdgeInsets.all(16.0),
    child: Text('Card Content'),
  ),
);
```

23. **ListTile**
```dart
ListTile(
  leading: Icon(Icons.album),
  title: Text('The Enchanted Nightingale'),
  subtitle: Text('Music by Julie Gable. Lyrics by Sidney Stein.'),
);
```

24. **Divider**
```dart
Divider(color: Colors.black),
```

25. **AppBar**
```dart
AppBar(
  title: Text('App Bar Title'),
);
```

26. **BottomNavigationBar**
```dart
BottomNavigationBar(
  items: const <BottomNavigationBarItem>[
    BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
    BottomNavigationBarItem(icon: Icon(Icons.business), label: 'Business'),
  ],
);
```

27. **Drawer**
```dart
Drawer(
  child: ListView(
    children: <Widget>[
      DrawerHeader(
        decoration: BoxDecoration(color: Colors.blue),
        child: Text('Drawer Header'),
      ),
      ListTile(
        title: Text('Item 1'),
        onTap: () {},
      ),
    ],
  ),
);
```

28. **FloatingActionButton**
```dart
FloatingActionButton(
  onPressed: () {},
  child: Icon(Icons.add),
);
```

29. **Scaffold**
```dart
Scaffold(
  appBar: AppBar(title: Text('Scaffold')),
  body: Center(child: Text('Hello, world!')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: Icon(Icons.add),
  ),
);
```

30. **MaterialApp**
```dart
MaterialApp(
  home: Scaffold(
    appBar: AppBar(title: Text('MaterialApp')),
    body: Center(child: Text('Hello, world!')),
  ),
);
```

31. **CupertinoApp**
```dart
CupertinoApp(
  home: CupertinoPageScaffold(
    navigationBar: CupertinoNavigationBar(
      middle: Text('CupertinoApp'),
    ),
    child: Center(child: Text('Hello, world!')),
  ),
);
```

32. **ScaffoldMessenger**
```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(content: Text('Hello Snackbar')),
);
```

33. **Theme**
```dart
Theme(
  data: ThemeData(primarySwatch: Colors.blue),
  child: Builder(
    builder: (context) {
      return Scaffold(
        appBar: AppBar(title: Text('Themed App')),
        body: Center(child: Text('Hello, world!')),
      );
    },
  ),
);
```

34. **FutureBuilder**
```dart
FutureBuilder<int>(
  future: Future.delayed(Duration(seconds: 2), () => 42),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return CircularProgressIndicator();
    } else if (snapshot.hasError) {
      return Text('Error: ${snapshot.error}');
    } else {
      return Text('Result: ${snapshot.data}');
    }
  },
);
```

35. **StreamBuilder**
```dart
StreamBuilder<int>(
  stream: Stream.periodic(Duration(seconds: 1), (count) => count),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return CircularProgressIndicator();
    } else if (snapshot.hasError) {
      return Text('Error: ${snapshot.error}');
    } else {
      return Text('Count: ${snapshot.data}');
    }
  },
);
```

36. **LayoutBuilder**
```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth > 600) {
      return Text('Large Screen');
    } else {
      return Text('Small Screen');
    }
  },
);
```

37. **Builder**
```dart
Builder(
  builder: (context) {
    return ElevatedButton(
      onPressed: () {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text('Hello Snackbar')),
        );
      },
      child: Text('Show Snackbar'),
    );
  },
);
```

38. **MediaQuery**
```dart
MediaQuery.of(context).size.width < 600
    ? Text('Small Screen')
    : Text('Large Screen'),
```

39. **Navigator**
```dart
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => SecondScreen()),
);
```

40. **PageView**
```dart
PageView(
  children: [
    Container(color: Colors.red),
    Container(color: Colors.green),
    Container(color: Colors.blue),
  ],
);
```

41. **TabBar**
```dart
TabBar(
  tabs: [
    Tab(icon: Icon(Icons.directions_car)),
    Tab(icon: Icon(Icons.directions_transit)),
    Tab(icon: Icon(Icons.directions_bike)),
  ],
);
```

42. **TabBarView**
```dart
TabBarView(
  children: [
    Icon(Icons.directions_car),
    Icon(Icons.directions_transit),
    Icon(Icons.directions_bike),
  ],
);
```

43. **DefaultTabController**
```dart
DefaultTabController(
  length: 3,
  child: Scaffold(
    appBar: AppBar(
      bottom: TabBar(
        tabs: [
          Tab(icon: Icon(Icons.directions_car)),
          Tab(icon: Icon(Icons.directions_transit)),
          Tab(icon: Icon(Icons.directions_bike)),
        ],
      ),
      title: Text('Tabs Demo'),
    ),
    body: TabBarView(
      children: [
        Icon(Icons.directions_car),
        Icon(Icons.directions_transit),
        Icon(Icons.directions_bike),
      ],
    ),
  ),
);
```

44. **Form**


```dart
Form(
  child: Column(
    children: [
      TextFormField(decoration: InputDecoration(labelText: 'Name')),
      ElevatedButton(onPressed: () {}, child: Text('Submit')),
    ],
  ),
);
```

45. **TextFormField**
```dart
TextFormField(
  decoration: InputDecoration(labelText: 'Enter your name'),
);
```

46. **ElevatedButton**
```dart
ElevatedButton(
  onPressed: () {},
  child: Text('ElevatedButton'),
);
```

47. **TextButton**
```dart
TextButton(
  onPressed: () {},
  child: Text('TextButton'),
);
```

48. **IconButton**
```dart
IconButton(
  icon: Icon(Icons.volume_up),
  onPressed: () {},
);
```

49. **FloatingActionButton**
```dart
FloatingActionButton(
  onPressed: () {},
  child: Icon(Icons.navigation),
);
```

50. **InkWell**
```dart
InkWell(
  onTap: () {},
  child: Container(
    padding: EdgeInsets.all(12.0),
    child: Text('InkWell'),
  ),
);
```

51. **GestureDetector**
```dart
GestureDetector(
  onTap: () {},
  child: Container(
    padding: EdgeInsets.all(12.0),
    child: Text('GestureDetector'),
  ),
);
```

52. **Draggable**
```dart
Draggable<String>(
  data: 'Hello',
  child: Container(
    color: Colors.blue,
    width: 100,
    height: 100,
    child: Center(child: Text('Drag me')),
  ),
  feedback: Container(
    color: Colors.blue.withOpacity(0.5),
    width: 100,
    height: 100,
    child: Center(child: Text('Dragging')),
  ),
);
```

53. **DragTarget**
```dart
DragTarget<String>(
  builder: (context, candidateData, rejectedData) {
    return Container(
      width: 100,
      height: 100,
      color: Colors.red,
      child: Center(child: Text('Drop here')),
    );
  },
  onAccept: (data) {
    ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('Accepted: $data')));
  },
);
```

54. **ReorderableListView**
```dart
ReorderableListView(
  onReorder: (oldIndex, newIndex) {},
  children: [
    ListTile(key: ValueKey('Item 1'), title: Text('Item 1')),
    ListTile(key: ValueKey('Item 2'), title: Text('Item 2')),
  ],
);
```

55. **Slider**
```dart
Slider(
  value: 50,
  min: 0,
  max: 100,
  onChanged: (value) {},
);
```

56. **Switch**
```dart
Switch(
  value: true,
  onChanged: (value) {},
);
```

57. **Checkbox**
```dart
Checkbox(
  value: true,
  onChanged: (value) {},
);
```

58. **Radio**
```dart
Radio<int>(
  value: 1,
  groupValue: 1,
  onChanged: (value) {},
);
```

59. **DropdownButton**
```dart
DropdownButton<String>(
  value: 'One',
  items: <String>['One', 'Two', 'Three'].map((String value) {
    return DropdownMenuItem<String>(
      value: value,
      child: Text(value),
    );
  }).toList(),
  onChanged: (value) {},
);
```

60. **PopupMenuButton**
```dart
PopupMenuButton<String>(
  onSelected: (value) {},
  itemBuilder: (context) => [
    PopupMenuItem(value: 'One', child: Text('One')),
    PopupMenuItem(value: 'Two', child: Text('Two')),
  ],
);
```

61. **AlertDialog**
```dart
AlertDialog(
  title: Text('Title'),
  content: Text('Content'),
  actions: [
    TextButton(onPressed: () {}, child: Text('OK')),
  ],
);
```

62. **SimpleDialog**
```dart
SimpleDialog(
  title: Text('Title'),
  children: [
    SimpleDialogOption(
      onPressed: () {},
      child: Text('Option 1'),
    ),
    SimpleDialogOption(
      onPressed: () {},
      child: Text('Option 2'),
    ),
  ],
);
```

63. **SnackBar**
```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(content: Text('Hello Snackbar')),
);
```

64. **BottomSheet**
```dart
showModalBottomSheet(
  context: context,
  builder: (context) {
    return Container(
      height: 200,
      child: Center(child: Text('Bottom Sheet')),
    );
  },
);
```

65. **ExpansionPanel**
```dart
ExpansionPanelList(
  expansionCallback: (panelIndex, isExpanded) {},
  children: [
    ExpansionPanel(
      headerBuilder: (context, isExpanded) {
        return ListTile(title: Text('Panel 1'));
      },
      body: ListTile(title: Text('Content 1')),
      isExpanded: true,
    ),
  ],
);
```

66. **ExpansionTile**
```dart
ExpansionTile(
  title: Text('ExpansionTile'),
  children: [
    ListTile(title: Text('Child 1')),
    ListTile(title: Text('Child 2')),
  ],
);
```

67. **ListBody**
```dart
ListBody(
  children: [
    Text('Item 1'),
    Text('Item 2'),
  ],
);
```

68. **Wrap**
```dart
Wrap(
  children: [
    Chip(label: Text('Chip 1')),
    Chip(label: Text('Chip 2')),
  ],
);
```

69. **AnimatedContainer**
```dart
AnimatedContainer(
  duration: Duration(seconds: 1),
  color: Colors.blue,
  width: 100,
  height: 100,
);
```

70. **AnimatedOpacity**
```dart
AnimatedOpacity(
  opacity: 0.5,
  duration: Duration(seconds: 1),
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

71. **AnimatedAlign**
```dart
AnimatedAlign(
  alignment: Alignment.topRight,
  duration: Duration(seconds: 1),
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

72. **AnimatedPositioned**
```dart
Stack(
  children: [
    AnimatedPositioned(
      left: 50,
      top: 50,
      duration: Duration(seconds: 1),
      child: Container(color: Colors.blue, width: 100, height: 100),
    ),
  ],
);
```

73. **AnimatedSize**
```dart
AnimatedSize(
  duration: Duration(seconds: 1),
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

74. **AnimatedDefaultTextStyle**
```dart
AnimatedDefaultTextStyle(
  style: TextStyle(fontSize: 24, color: Colors.blue),
  duration: Duration(seconds: 1),
  child: Text('Animated Text'),
);
```

75. **AnimatedPhysicalModel**
```dart
AnimatedPhysicalModel(
  shape: BoxShape.rectangle,
  elevation: 6.0,
  color: Colors.blue,
  shadowColor: Colors.black,
  duration: Duration(seconds: 1),
  child: Container(width: 100, height: 100),
);
```

76. **AnimatedBuilder**
```dart
AnimationController _controller = AnimationController(
  duration: const Duration(seconds: 2),
  vsync: this,
);
Animation<double> _animation = Tween<double>(begin: 0, end: 1).animate(_controller);

AnimatedBuilder(
  animation: _animation,
  builder: (context, child) {
    return Opacity(opacity: _animation.value, child: child);
  },
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

77. **AnimatedSwitcher**
```dart
AnimatedSwitcher(
  duration: Duration(seconds: 1),
  child: Container(key: ValueKey<int>(1), color: Colors.blue, width: 100, height: 100),
);
```

78. **AnimatedList**
```dart
AnimatedList(
  initialItemCount: 1,
  itemBuilder: (context, index, animation) {
    return SizeTransition(
      sizeFactor: animation,
      child: ListTile(title: Text('Item $index')),
    );
  },
);
```

79. **AnimatedGrid**
```dart
AnimatedGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 2),
  initialItemCount: 2,
  itemBuilder: (context, index, animation) {
    return ScaleTransition(
      scale: animation,
      child: Container(color: Colors.blue, width: 100, height: 100),
    );
  },
);
```

80. **Hero**
```dart
Hero(
  tag: 'hero-tag',
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

81. **FadeTransition**
```dart
FadeTransition(
  opacity: _animation,
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

82. **ScaleTransition**
```dart
ScaleTransition(
  scale: _animation,
  child: Container

(color: Colors.blue, width: 100, height: 100),
);
```

83. **RotationTransition**
```dart
RotationTransition(
  turns: _animation,
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

84. **SlideTransition**
```dart
SlideTransition(
  position: Tween<Offset>(begin: Offset.zero, end: Offset(1, 0)).animate(_animation),
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

85. **DecoratedBoxTransition**
```dart
DecoratedBoxTransition(
  decoration: DecorationTween(
    begin: BoxDecoration(color: Colors.blue),
    end: BoxDecoration(color: Colors.red),
  ).animate(_animation),
  child: Container(width: 100, height: 100),
);
```

86. **DefaultTextStyleTransition**
```dart
DefaultTextStyleTransition(
  style: TextStyleTween(
    begin: TextStyle(color: Colors.blue, fontSize: 20),
    end: TextStyle(color: Colors.red, fontSize: 40),
  ).animate(_animation),
  child: Text('Hello, world!'),
);
```

87. **AlignTransition**
```dart
AlignTransition(
  alignment: AlignmentTween(
    begin: Alignment.topLeft,
    end: Alignment.bottomRight,
  ).animate(_animation),
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

88. **PositionedTransition**
```dart
Stack(
  children: [
    PositionedTransition(
      rect: RelativeRectTween(
        begin: RelativeRect.fromLTRB(0, 0, 0, 0),
        end: RelativeRect.fromLTRB(50, 50, 0, 0),
      ).animate(_animation),
      child: Container(color: Colors.blue, width: 100, height: 100),
    ),
  ],
);
```

89. **TweenAnimationBuilder**
```dart
TweenAnimationBuilder<double>(
  tween: Tween<double>(begin: 0, end: 1),
  duration: Duration(seconds: 1),
  builder: (context, value, child) {
    return Opacity(opacity: value, child: child);
  },
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

90. **ClipRect**
```dart
ClipRect(
  child: Align(
    alignment: Alignment.topLeft,
    widthFactor: 0.5,
    heightFactor: 0.5,
    child: Container(color: Colors.blue, width: 100, height: 100),
  ),
);
```

91. **ClipRRect**
```dart
ClipRRect(
  borderRadius: BorderRadius.circular(20.0),
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

92. **ClipOval**
```dart
ClipOval(
  child: Container(color: Colors.blue, width: 100, height: 100),
);
```

93. **CustomPaint**
```dart
CustomPaint(
  painter: MyPainter(),
  child: Container(width: 100, height: 100),
);

class MyPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.blue
      ..strokeWidth = 5;
    canvas.drawLine(Offset(0, 0), Offset(size.width, size.height), paint);
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) => false;
}
```

94. **CustomScrollView**
```dart
CustomScrollView(
  slivers: [
    SliverAppBar(
      title: Text('CustomScrollView'),
      floating: true,
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 20,
      ),
    ),
  ],
);
```

95. **SliverList**
```dart
SliverList(
  delegate: SliverChildBuilderDelegate(
    (context, index) => ListTile(title: Text('Item $index')),
    childCount: 20,
  ),
);
```

96. **SliverGrid**
```dart
SliverGrid(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 2),
  delegate: SliverChildBuilderDelegate(
    (context, index) => Container(color: Colors.blue, height: 100),
    childCount: 20,
  ),
);
```

97. **SliverAppBar**
```dart
CustomScrollView(
  slivers: [
    SliverAppBar(
      title: Text('SliverAppBar'),
      floating: true,
    ),
    SliverList(
      delegate: SliverChildBuilderDelegate(
        (context, index) => ListTile(title: Text('Item $index')),
        childCount: 20,
      ),
    ),
  ],
);
```

98. **SliverPadding**
```dart
CustomScrollView(
  slivers: [
    SliverPadding(
      padding: EdgeInsets.all(8.0),
      sliver: SliverList(
        delegate: SliverChildBuilderDelegate(
          (context, index) => ListTile(title: Text('Item $index')),
          childCount: 20,
        ),
      ),
    ),
  ],
);
```

99. **SliverFillRemaining**
```dart
CustomScrollView(
  slivers: [
    SliverFillRemaining(
      child: Center(child: Text('SliverFillRemaining')),
    ),
  ],
);
```

100. **NotificationListener**
```dart
NotificationListener<ScrollNotification>(
  onNotification: (scrollNotification) {
    // Handle scroll notification
    return true;
  },
  child: ListView(
    children: List.generate(20, (index) => ListTile(title: Text('Item $index'))),
  ),
);
```




شرح مبسط للـ Widgets في Flutter مع التفاصيل

1. Container:

غلاف أساسي لأي عنصر في Flutter.

يُستخدم لضبط خصائص العناصر مثل اللون، والحجم، والهامش، والتدرج، والزوايا.

مثال: Container(color: Colors.blue, child: Text("نص")).

2. Column:

يرتب العناصر بشكل عمودي.

خصائص: mainAxisAlignment (لترتيب العناصر عمودياً)، crossAxisAlignment (لترتيب العناصر أفقياً).

مثال: Column(children: [Text("نص 1"), Text("نص 2")]).

3. Row:

يرتب العناصر بشكل أفقي.

خصائص: mainAxisAlignment (لترتيب العناصر أفقياً)، crossAxisAlignment (لترتيب العناصر عمودياً).

مثال: Row(children: [Text("نص 1"), Text("نص 2")]).

4. Stack:

يضع العناصر فوق بعضها البعض.

خصائص: alignment (لضبط موقع العناصر)، overflow (لتحديد سلوك التداخل).

مثال: Stack(children: [Text("نص 1"), Text("نص 2")]).

5. ListView:

يعرض قائمة من العناصر.

خصائص: scrollDirection (اتجاه التمرير)، reverse (تغيير ترتيب العناصر).

مثال: ListView.builder(itemCount: 10, itemBuilder: (context, index) => Text("عنصر $index")).

6. GridView:

يعرض مجموعة من العناصر في شبكة.

خصائص: gridDelegate (لضبط شكل الشبكة)، crossAxisCount (عدد الأعمدة).

مثال: GridView.count(crossAxisCount: 2, children: [Text("عنصر 1"), Text("عنصر 2")]).

7. SingleChildScrollView:

يُمكّن من التمرير لعنصر واحد كبير.

مثال: SingleChildScrollView(child: Text("نص طويل جدًا")).

8. SizedBox:

يحدد حجم العنصر بدقة.

خصائص: width (العرض)، height (الارتفاع).

مثال: SizedBox(width: 100, height: 50, child: Text("نص")).

9. Padding:

يُضيف هامش حول العنصر.

خصائص: padding (قيمة الهامش).

مثال: Padding(padding: EdgeInsets.all(16), child: Text("نص")).

10. Center:

يُركز العنصر في وسط المساحة المتاحة.

مثال: Center(child: Text("نص")).

11. Align:

يُحاذي العنصر في موقع معين.

خصائص: alignment (موضع التحاذي).

مثال: Align(alignment: Alignment.bottomRight, child: Text("نص")).

12. Positioned:

يُحدد موقع العنصر بدقة داخل Stack.

خصائص: left, top, right, bottom.

مثال: Positioned(left: 10, top: 10, child: Text("نص")).

13. Expanded:

يُوسع عنصر ليملأ المساحة المتبقية داخل Row أو Column.

مثال: Expanded(child: Text("نص")).

14. Flexible:

يُمكن عنصر من التغيير في حجمه حسب المساحة المتاحة.

خصائص: flex (نسبة التوسع).

مثال: Flexible(flex: 2, child: Text("نص")).

15. Spacer:

يُضيف مساحة فارغة داخل Row أو Column لفصل العناصر.

مثال: Row(children: [Text("نص 1"), Spacer(), Text("نص 2")]).

16. Text:

يُعرض نص.

خصائص: style (تنسيق النص)، textAlign (محاذاة النص).

مثال: Text("نص", style: TextStyle(fontSize: 20, color: Colors.red)).

17. RichText:

يُعرض نص مع تنسيق متعدد.

خصائص: text (النص)، textAlign (محاذاة النص)، maxLines (عدد الأسطر).

مثال: RichText(text: TextSpan(text: "نص", style: TextStyle(fontSize: 20, color: Colors.red))).

18. Icon:

يُعرض رمز.

خصائص: icon (الرمز)، size (حجم الرمز).

مثال: Icon(Icons.favorite, size: 30).

19. Image:

يُعرض صورة.

خصائص: image (الموقع)، width (العرض)، height (الارتفاع).

مثال: Image.asset('assets/image.png').

20. CircleAvatar:

يُعرض صورة دائرية.

خصائص: backgroundImage (صورة الخلفية)، radius (نصف القطر).

مثال: CircleAvatar(backgroundImage: AssetImage('assets/image.png'), radius: 30).

21. Chip:

يُعرض عنصر صغير مستطيل يحتوي على نص أو رمز.

خصائص: label (النص)، avatar (الرمز).

مثال: Chip(label: Text("عنوان"), avatar: CircleAvatar(child: Icon(Icons.person))).

22. Card:

يُعرض بطاقة لتقديم محتوى معين.

خصائص: child (المحتوى)، elevation (الظل).

مثال: Card(child: Text("محتوى")).

23. ListTile:

يُعرض عنصر قائمة بتصميم قياسي.

خصائص: title (العنوان)، subtitle (الوصف).

مثال: ListTile(title: Text("عنوان"), subtitle: Text("وصف")).

24. Divider:

يُعرض خط فواصل.

خصائص: color (لون الخط).

مثال: Divider().

25. AppBar:

شريط علوي يُستخدم للعنوان وأزرار التنقل.

خصائص: title (العنوان)، actions (أزرار التنقل).

مثال: AppBar(title: Text("عنوان")).

26. BottomNavigationBar:

شريط سفلي يُستخدم لأزرار التنقل.

خصائص: items (أزرار التنقل).

مثال: BottomNavigationBar(items: [BottomNavigationBarItem(icon: Icon(Icons.home), label: "الرئيسية")]).

27. Drawer:

درج جانبي يُستخدم لعرض خيارات التنقل.

خصائص: child (المحتوى).

مثال: Drawer(child: ListView(children: [ListTile(title: Text("عن التطبيق"))])).

28. FloatingActionButton:

زر عائم يُستخدم لتنفيذ عمل معين.

خصائص: onPressed (وظيفة يتم تنفيذها عند الضغط).

مثال: FloatingActionButton(onPressed: () => print("ضغط على الزر"), child: Icon(Icons.add)).

29. Scaffold:

يُستخدم لإنشاء هيكل التطبيق الأساسي.

خصائص: appBar (الشريط العلوي)، body (المحتوى الأساسي).

مثال: Scaffold(appBar: AppBar(title: Text("عنوان")), body: Text("محتوى")).

30. MaterialApp:

يُستخدم لإنشاء تطبيق Flutter.

خصائص: home (الشاشة الرئيسية).

مثال: MaterialApp(home: Scaffold(appBar: AppBar(title: Text("عنوان")), body: Text("محتوى"))).

31. CupertinoApp:

يُستخدم لإنشاء تطبيق Flutter بتصميم iOS.

خصائص: home (الشاشة الرئيسية).

مثال: CupertinoApp(home: CupertinoPageScaffold(navigationBar: CupertinoNavigationBar(middle: Text("عنوان")), child: Text("محتوى"))).

32. ScaffoldMessenger:

يُستخدم لعرض الرسائل والتنبيهات.

خصائص: showSnackBar (لعرض SnackBar).

مثال: ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text("رسالة"))).

33. Theme:

يُستخدم لتغيير مظهر التطبيق.

خصائص: primaryColor (اللون الأساسي)، textTheme (تنسيق النص).

مثال: ThemeData(primaryColor: Colors.red, textTheme: TextTheme(headline1: TextStyle(fontSize: 20, color: Colors.black))).

34. FutureBuilder:

يُستخدم لإنشاء واجهة تُظهر البيانات بعد تحميلها من خادم.

خصائص: future (الوظيفة التي تُعيد البيانات)، builder (الوظيفة التي تُنشئ الواجهة).

مثال: FutureBuilder(future: fetchData(), builder: (context, snapshot) => ... ).

35. StreamBuilder:

يُستخدم لإنشاء واجهة تُظهر البيانات التي تتغير بمرور الوقت.

خصائص: stream (الدفق الذي يُصدر البيانات)، builder (الوظيفة التي تُنشئ الواجهة).

مثال: StreamBuilder(stream: fetchDataStream(), builder: (context, snapshot) => ... ).

36. LayoutBuilder:

يُستخدم لإنشاء واجهة تستجيب لحجم الشاشة.

خصائص: builder (الوظيفة التي تُنشئ الواجهة).

مثال: LayoutBuilder(builder: (context, constraints) => ... ).

37. Builder:

يُستخدم لإنشاء واجهة باستخدام البيانات من BuildContext.

خصائص: builder (الوظيفة التي تُنشئ الواجهة).

مثال: Builder(builder: (context) => ... ).

38. MediaQuery:

يُستخدم للحصول على معلومات عن الشاشة مثل حجمها واتجاهها.

خصائص: size (حجم الشاشة)، orientation (اتجاه الشاشة).

مثال: MediaQuery.of(context).size.width.

39. Navigator:

يُستخدم للتنقل بين الشاشات.

خصائص: push (للدفع بشاشة جديدة)، pop (للرجوع إلى الشاشة السابقة).

مثال: Navigator.push(context, MaterialPageRoute(builder: (context) => NewScreen())).

40. PageView:

يُستخدم لعرض مجموعة من الشاشات.

خصائص: controller (للسيطرة على التمرير)، onPageChanged (للحصول على رقم الشاشة الحالية).

مثال: PageView(controller: PageController(), children: [Screen1(), Screen2()]).

41. TabBar:

شريط يُستخدم لعرض علامات تبويب.

خصائص: tabs (علامات التبويب).

مثال: TabBar(tabs: [Tab(text: "عنوان 1"), Tab(text: "عنوان 2")]).

42. TabBarView:

يُستخدم لعرض المحتوى المقابل لعلامات التبويب.

خصائص: children (محتوى كل علامة تبويب).

مثال: TabBarView(children: [Text("محتوى 1"), Text("محتوى 2")]).

43. DefaultTabController:

يُستخدم لإنشاء TabBar و TabBarView معًا.

خصائص: length (عدد علامات التبويب).

مثال: DefaultTabController(length: 2, child: ... ).

44. Form:

يُستخدم لإنشاء نموذج.

خصائص: child (المحتوى).

مثال: Form(child: ... ).

45. TextFormField:

حقل نصي يُستخدم في النماذج.

خصائص: initialValue (القيمة الافتراضية)، validator (للتحقق من صحة الإدخال).

مثال: TextFormField(initialValue: "نص").

46. ElevatedButton:

زر متميز يُستخدم في النماذج.

خصائص: onPressed (وظيفة يتم تنفيذها عند الضغط).

مثال: ElevatedButton(onPressed: () => print("ضغط على الزر"), child: Text("زر")).

47. TextButton:

زر بسيط يُستخدم في النماذج.

خصائص: onPressed (وظيفة يتم تنفيذها عند الضغط).

مثال: TextButton(onPressed: () => print("ضغط على الزر"), child: Text("زر")).

48. IconButton:

زر يُستخدم لعرض رمز.

خصائص: onPressed (وظيفة يتم تنفيذها عند الضغط).

مثال: IconButton(onPressed: () => print("ضغط على الزر"), icon: Icon(Icons.add)).

49. FloatingActionButton:

زر عائم يُستخدم لتنفيذ عمل معين.

خصائص: onPressed (وظيفة يتم تنفيذها عند الضغط).

مثال: FloatingActionButton(onPressed: () => print("ضغط على الزر"), child: Icon(Icons.add)).

50. InkWell:

يُمكنك من إنشاء تأثير النقر على أي عنصر.

خصائص: onTap (وظيفة يتم تنفيذها عند النقر).

مثال: InkWell(onTap: () => print("نقر على العنصر"), child: Text("نص")).

51. GestureDetector:

يُمكنك من إنشاء تأثير النقر أو السحب أو التمرير على أي عنصر.

خصائص: onTap (النقر)، onLongPress (الضغط الطويل)، onPanStart (بداية السحب).

مثال: GestureDetector(onTap: () => print("نقر على العنصر"), child: Text("نص")).

52. Draggable:

يُمكنك من سحب عنصر.

خصائص: child (العنصر)، onDragStarted (بداية السحب).

مثال: Draggable(child: Text("نص"), onDragStarted: () => print("بدء السحب")).

53. DragTarget:

يُمكنك من استقبال عنصر تم سحبه.

خصائص: onAccept (وظيفة يتم تنفيذها عند استقبال العنصر).

مثال: DragTarget(onAccept: (data) => print("تم استقبال العنصر")).

54. ReorderableListView:

قائمة تُمكّنك من إعادة ترتيب العناصر.

خصائص: onReorder (وظيفة يتم تنفيذها عند إعادة الترتيب).

مثال: ReorderableListView(onReorder: (oldIndex, newIndex) => ... ).

55. Slider:

شريط انزلاق يُستخدم لاختيار قيمة.

خصائص: value (القيمة الحالية)، onChanged (وظيفة يتم تنفيذها عند تغيير القيمة).

مثال: Slider(value: 0.5, onChanged: (value) => ... ).

56. Switch:

مفتاح يُستخدم للتبديل بين حالتين.

خصائص: value (الحالة الحالية)، onChanged (وظيفة يتم تنفيذها عند تغيير الحالة).

مثال: Switch(value: true, onChanged: (value) => ... ).

57. Checkbox:

خانة الاختيار يُستخدم لتحديد خيار.

خصائص: value (الحالة الحالية)، onChanged (وظيفة يتم تنفيذها عند تغيير الحالة).

مثال: Checkbox(value: true, onChanged: (value) => ... ).

58. Radio:

زر اختيار يُستخدم لاختيار خيار واحد من مجموعة.

خصائص: value (قيمة الخيار)، groupValue (قيمة المجموعة).

مثال: Radio(value: "خيار 1", groupValue: selectedValue, onChanged: (value) => ... ).

59. DropdownButton:

قائمة منسدلة تُستخدم لاختيار قيمة من قائمة.

خصائص: value (القيمة الحالية)، items (قائمة الخيارات).

مثال: DropdownButton(value: "خيار 1", items: [DropdownMenuItem(value: "خيار 1", child: Text("خيار 1"))]).

60. PopupMenuButton:

قائمة منسدلة تظهر عند الضغط على زر.

خصائص: itemBuilder (الوظيفة التي تُنشئ قائمة الخيارات).

مثال: PopupMenuButton(itemBuilder: (context) => [PopupMenuItem(value: "خيار 1", child: Text("خيار 1"))]).

61. AlertDialog:

مربع حوار يُستخدم لعرض رسالة أو طلب الإدخال.

خصائص: title (العنوان)، content (المحتوى).

مثال: showDialog(context: context, builder: (context) => AlertDialog(title: Text("عنوان"), content: Text("محتوى"))).

62. SimpleDialog:

مربع حوار يُستخدم لعرض مجموعة من الخيارات.

خصائص: children (قائمة الخيارات).

مثال: showDialog(context: context, builder: (context) => SimpleDialog(children: [SimpleDialogOption(child: Text("خيار 1"))])).

63. SnackBar:

رسالة قصيرة تُظهر في أسفل الشاشة.

خصائص: content (المحتوى).

مثال: ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text("رسالة"))).

64. BottomSheet:

لوحة تُظهر في أسفل الشاشة.

خصائص: onClosing (وظيفة يتم تنفيذها عند إغلاق اللوحة).

مثال: showModalBottomSheet(context: context, builder: (context) => ... ).

65. ExpansionPanel:

لوحة تُمكن من طيها أو فتحها.

خصائص: headerBuilder (الوظيفة التي تُنشئ رأس اللوحة)، body (المحتوى).

مثال: ExpansionPanel(headerBuilder: (context) => ... , body: ... ).

66. ExpansionTile:

لوحة تُمكن من طيها أو فتحها.

خصائص: title (العنوان)، children (المحتوى).

مثال: ExpansionTile(title: Text("عنوان"), children: [Text("محتوى")]).

67. ListBody:

يُستخدم لترتيب العناصر في قائمة.

خصائص: children (قائمة العناصر).

مثال: ListBody(children: [Text("عنصر 1"), Text("عنصر 2")]).

68. Wrap:

يُستخدم لترتيب العناصر في صفوف متعددة.

خصائص: direction (اتجاه الترتيب)، spacing (المسافة بين العناصر).

مثال: Wrap(children: [Text("عنصر 1"), Text("عنصر 2")]).

69. AnimatedContainer:

يُستخدم لإنشاء حاوية تتحرك بشكل سلس.

خصائص: duration (مدة التحريك)، curve (منحنى التحريك).

مثال: AnimatedContainer(duration: Duration(seconds: 1), curve: Curves.easeInOut, ... ).

70. AnimatedOpacity:

يُستخدم لتغيير شفافية عنصر بشكل سلس.

خصائص: duration (مدة التحريك)، curve (منحنى التحريك).

مثال: AnimatedOpacity(duration: Duration(seconds: 1), curve: Curves.easeInOut, ... ).

71. AnimatedAlign:

يُستخدم لتحريك عنصر بشكل سلس إلى موقع معين.

خصائص: duration (مدة التحريك)، curve (منحنى التحريك).

مثال: AnimatedAlign(duration: Duration(seconds: 1), curve: Curves.easeInOut, ... ).

72. AnimatedPositioned:

يُستخدم لتحريك عنصر بشكل سلس داخل Stack.

خصائص: duration (مدة التحريك)، curve (منحنى التحريك).

مثال: AnimatedPositioned(duration: Duration(seconds: 1), curve: Curves.easeInOut, ... ).

73. AnimatedSize:

يُستخدم لتغيير حجم عنصر بشكل سلس.

خصائص: duration (مدة التحريك)، curve (منحنى التحريك).

مثال: AnimatedSize(duration: Duration(seconds: 1), curve: Curves.easeInOut, ... ).

74. AnimatedDefaultTextStyle:

يُستخدم لتغيير نمط النص بشكل سلس.

خصائص: duration (مدة التحريك)، curve (منحنى التحريك).

مثال: AnimatedDefaultTextStyle(duration: Duration(seconds: 1), curve: Curves.easeInOut, ... ).

75. AnimatedPhysicalModel:

يُستخدم لتغيير نموذج العنصر المادي بشكل سلس.

خصائص: duration (مدة التحريك)، curve (منحنى التحريك).

مثال: AnimatedPhysicalModel(duration: Duration(seconds: 1), curve: Curves.easeInOut, ... ).

76. AnimatedBuilder:

يُستخدم لإنشاء رسوم متحركة مُخصصة.

خصائص: `animation
