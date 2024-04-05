class LightV001 extends StatefulWidget {
  final Device device;

  const LightV001({Key? key, required this.device}) : super(key: key);
  @override
  _LightV001State createState() => _LightV001State();
}

class _LightV001State extends State<LightV001> with TickerProviderStateMixin {
  final WebSocketManager _webSocket = WebSocketManager();
  late String deviceIP;
  late Stream<dynamic> _stream;

  bool isActive = false;
  int speed = 1;
  double percent = 50;
  double progressVal = 0.49;
  int selectedOption = 0;
  Color currentColor = const Color(0xff443a49);

  double fpsValue = 100;
  double sparkingValue = 60;
  double coolingValue = 20;
  Color pickerColor = const Color(0xff443a49);

  var activeColor = Rainbow(spectrum: [
    const Color(0xFF33C0BA),
    const Color(0xFF1086D4),
    const Color(0xFF6D04E2),
    const Color(0xFFC421A0),
    const Color(0xFFE4262F)
  ], rangeStart: 0.0, rangeEnd: 1.0);

  @override
  void initState() {
    super.initState();
    deviceIP = widget.device.deviceIP;
    _webSocket.connect(deviceIP);
    _stream = _webSocket.stream(deviceIP)!;
    _stream.listen((message) {
      print("--------------------------------------$message");
      // Map<String, dynamic> messageMap = jsonDecode(message);
      // percent = messageMap['B'].toDouble();
      // print(percent);
    }, onError: (error) {
      print(error);
    });
  }

  @override
  void dispose() {
    _webSocket.disconnect(widget.device.deviceIP);
    super.dispose();
  }


  void selectedColorChangeColor(Color color) {
    final hexColor = '#${color.value.toRadixString(16).padLeft(8,'0').substring(2)}';
    print("new color: $hexColor");
    _webSocket.send(deviceIP, {"type": "E","value": 35, "option": {"color": hexColor}});
    setState(() {
      currentColor = color;
    });
  }

  void fireStripChangeColor(Color color) {
    setState(() => pickerColor = color);
  }

  void _updateFpsValueFireStrip(double newValue) {
    setState(() {
      fpsValue = newValue;
    });
  }

  void _updateSparkingValueFireStrip(double newValue) {
    setState(() {
      sparkingValue = newValue;
    });
  }

  void _updateCoolingValueFireStrip(double newValue) {
    setState(() {
      coolingValue = newValue;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(
        width: MediaQuery.of(context).size.width,
        height: MediaQuery.of(context).size.height,
        decoration: BoxDecoration(
          gradient: LinearGradient(
              begin: Alignment.topCenter,
              end: Alignment.bottomCenter,
              colors: <Color>[
                Colors.white,
                activeColor[progressVal].withOpacity(0.5),
                activeColor[progressVal]
              ]),
        ),
        child: AnimatedBackground(
          behaviour: RandomParticleBehaviour(
              options: ParticleOptions(
                baseColor: const Color(0xFFFFFFFF),
                opacityChangeRate: 0.25,
                minOpacity: 0.1,
                maxOpacity: 0.3,
                spawnMinSpeed: speed * 60.0,
                spawnMaxSpeed: speed * 120,
                spawnMinRadius: 2.0,
                spawnMaxRadius: 5.0,
                particleCount: isActive ? speed * 150 : 0,
              )),
          vsync: this,
          child: SafeArea(
            child: Padding(
              padding: const EdgeInsets.fromLTRB(15, 15, 15, 0),
              child:
              DefaultTabController(
                length: 2,
                child: Scaffold(
                  backgroundColor: Colors.transparent,
                  appBar: AppBar(
                    backgroundColor: Colors.transparent,
                    bottom: const TabBar(
                      tabs: [
                        Tab(
                          icon: Row(
                            mainAxisAlignment: MainAxisAlignment.center,
                            children: [
                              Icon(Icons.settings),
                              SizedBox(width: 8),
                              Text('Setting'),
                            ],
                          ),
                        ),
                        Tab(
                          icon: Row(
                            mainAxisAlignment: MainAxisAlignment.center,
                            children: [
                              Icon(Icons.lightbulb_outline),
                              SizedBox(width: 8),
                              Text('Effect'),
                            ],
                          ),
                        ),
                      ],
                    ),

                    title: const Text('Light V001'),
                  ),
                  body:  Padding( // Thêm Padding ở đây
                        padding: const EdgeInsets.only(top: 10), // Đặt khoảng cách bạn muốn
                    child:TabBarView(
                      children: [
                        SingleChildScrollView(
                          child: Column(
                            children: [
                              const SizedBox(height: 38),
                              slider(),
                              const SizedBox(height: 38),
                              controls(),
                            ],
                          ),
                        ),
                        SingleChildScrollView(
                          child: options(),
                        ),
                      ],
                    ),
                  ),
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }

  Widget options() {
    List<Map<String, dynamic>> names = [
      {'name': 'rainbow fade', 'hasIconButton': false},
      {'name': 'rainbow loop', 'hasIconButton': false},
      {'name': 'rainbow loop fade', 'hasIconButton': false},
      {'name': 'rainbow vertical', 'hasIconButton': false},
      {'name': 'random march', 'hasIconButton': false},
      {'name': 'rgb propeller', 'hasIconButton': false},
      {'name': 'fire', 'hasIconButton': false},
      {'name': 'blue fire', 'hasIconButton': false},
      {'name': 'random burst', 'hasIconButton': false},
      {'name': 'flicker', 'hasIconButton': false},
      {'name': 'random color pop', 'hasIconButton': false},
      {'name': 'sparkle', 'hasIconButton': false},
      {'name': 'color bounce', 'hasIconButton': false},
      {'name': 'color bounce fade', 'hasIconButton': false},
      {'name': 'blue red bounce', 'hasIconButton': false},
      {'name': 'rotating red blue', 'hasIconButton': false},
      {'name': 'matrix', 'hasIconButton': false},
      {'name': 'radiation', 'hasIconButton': false},
      {'name': 'pacman', 'hasIconButton': false},
      {'name': 'pop horizontal', 'hasIconButton': false},
      {'name': 'snow sparkle', 'hasIconButton': false},
      {'name': 'rwb march', 'hasIconButton': false},
      {'name': 'flame', 'hasIconButton': false},
      {'name': 'theater chase', 'hasIconButton': false},
      {'name': 'strobe', 'hasIconButton': false},
      {'name': 'police blinker', 'hasIconButton': false},
      {'name': 'kitt', 'hasIconButton': false},
      {'name': 'rule', 'hasIconButton': false},
      {'name': 'fade vertical', 'hasIconButton': false},
      {'name': 'fade to center', 'hasIconButton': false},
      {'name': 'runner chameleon', 'hasIconButton': false},
      {'name': 'blende', 'hasIconButton': false},
      {'name': 'blende 2', 'hasIconButton': false},
      {'name': 'fire 2', 'hasIconButton': true},
      {'name': 'Select Color', 'hasIconButton': true}
    ];
    List<Widget> optionWidgets = [];
    for (int i = 0; i < names.length; i++) {
      optionWidgets.add(
        Container(
          decoration: BoxDecoration(
            color: selectedOption == i ? Colors.blue.withOpacity(0.3) : null, // Màu của item khi được chọn
            borderRadius: selectedOption == i ? BorderRadius.circular(10) : null, // Bo góc
          ),
          child: ListTile(
            leading: Text('${i+1}', style: const TextStyle(color: Colors.white)),
            title: GestureDetector(
              onTap: () {
                _webSocket.send(deviceIP, {"type": "E","value": i+1});
                setState(() {
                  selectedOption = i;
                });
              },
              child: Text(names[i]['name'], style: const TextStyle(color: Colors.white)), // Sử dụng tên từ danh sách
            ),
            trailing: names[i]['hasIconButton'] ? IconButton( // Kiểm tra nếu tên có IconButton
              icon: const Icon(Icons.settings, color: Colors.white),
              onPressed: () {
                showModalBottomSheet(
                  context: context,
                  isScrollControlled: true,
                  backgroundColor: Colors.transparent,
                  // Đặt màu nền trong suốt để hiển thị góc bo
                  builder: (BuildContext context) {
                    return Container(
                      height: MediaQuery.of(context).size.height * 0.8,
                      decoration: const BoxDecoration(
                        color: Colors.white,
                        borderRadius: BorderRadius.only( // Bo góc trên cùng
                          topLeft: Radius.circular(20),
                          topRight: Radius.circular(20),
                        ),
                      ),
                      child: Column(
                        children: <Widget>[
                          Padding(
                            padding: const EdgeInsets.all(10.0),
                            child: Row(
                              mainAxisAlignment: MainAxisAlignment.spaceBetween,
                              children: <Widget>[
                                const Text('Setup', style: TextStyle(color: Colors.black,
                                    fontSize: 20, fontWeight: FontWeight.bold)),
                                IconButton(
                                  icon: const Icon(Icons.close),
                                  onPressed: () {
                                    Navigator.pop(context);
                                  },
                                ),
                              ],
                            ),
                          ),
                          if(i == 34)
                            SingleChildScrollView(
                              child: ColorPicker(
                                pickerColor: currentColor,
                                paletteType: PaletteType.hsvWithHue,
                                onColorChanged: selectedColorChangeColor,
                              ),
                            ),
                          if(i == 33)
                             SingleChildScrollView(
                          child: Column(
                              children: [
                                TextButton(
                                  onPressed: () {
                                    showDialog(
                                      context: context,
                                      builder: (BuildContext context) {
                                        return Theme(
                                          data: Theme.of(context).copyWith(dialogBackgroundColor: SHColors.cardColor),
                                          child: AlertDialog(
                                            title: const Text('Pick a color!'),
                                            content: SingleChildScrollView(
                                              child: ColorPicker(
                                                pickerColor: pickerColor,
                                                onColorChanged: fireStripChangeColor,
                                              ),
                                            ),
                                            actions: <Widget>[
                                              TextButton(
                                                child: const Text('Got it'),
                                                onPressed: () {
                                                  Navigator.of(context).pop();
                                                },
                                              ),
                                            ],
                                          ),
                                        );
                                      },
                                    );
                                  },
                                  child: const Text('Change Color'),
                                ),
                                Row(
                                  children: <Widget>[
                                    Center(
                                      child: SizedBox(
                                        height: 200, // Đặt chiều cao mặc định
                                        child: GradientBar(
                                          onColorsChanged: (colors) {
                                            Map<String, dynamic> colorData = {};
                                            for (int i = 0; i < colors.length; i++) {
                                              colorData["cs$i"] = {
                                                "R": colors[i].red,
                                                "G": colors[i].green,
                                                "B": colors[i].blue,
                                              };
                                            }
                                            _webSocket.send(deviceIP, {"type": "E","value": 34, "option": {"colors": colorData}});
                                          },
                                        ),
                                      ),
                                    )
                                  ],
                                ),
                                Row(
                                  children: [
                                    Expanded(
                                      child: Column(
                                        crossAxisAlignment: CrossAxisAlignment.start,
                                        children: [
                                          Text('FPS: ${fpsValue.toInt()}'),
                                          Slider(
                                            value: fpsValue,
                                            min: 5,
                                            max: 200,
                                            divisions: 195,
                                            onChanged: (double newValue) {
                                              _updateFpsValueFireStrip(newValue);
                                              _webSocket.send(deviceIP, {"type": "E","value": 34, "option": {"fps": newValue}});
                                            },
                                          ),
                                        ],
                                      ),
                                    ),
                                  ],
                                ),
                                Row(
                                  children: [
                                    Expanded(
                                      child: Column(
                                        crossAxisAlignment: CrossAxisAlignment.start,
                                        children: [
                                          Text('Sparking: ${sparkingValue.toInt()}'),
                                          Slider(
                                            value: sparkingValue,
                                            min: 5,
                                            max: 250,
                                            divisions: 245,
                                            onChanged: (double newValue) {
                                              _updateSparkingValueFireStrip(newValue);
                                              _webSocket.send(deviceIP, {"type": "E","value": 34, "option": {"sparking": newValue}});
                                            },
                                          ),
                                        ],
                                      ),
                                    ),
                                  ],
                                ),
                                Row(
                                  children: [
                                    Expanded(
                                      child: Column(
                                        crossAxisAlignment: CrossAxisAlignment.start,
                                        children: [
                                          Text('Cooling: ${coolingValue.toInt()}'),
                                          Slider(
                                            value: coolingValue,
                                            min: 0,
                                            max: 100,
                                            divisions: 100,
                                            onChanged: (double newValue) {
                                              _updateCoolingValueFireStrip(newValue);
                                              _webSocket.send(deviceIP, {"type": "E","value": 34, "option": {"cooling": newValue}});
                                            },
                                          ),
                                        ],
                                      ),
                                    ),
                                  ],
                                ),

                              ],
                          ),
                            ),
                        ],
                      ),
                    );
                  },
                );
              },
            ) : null, // Nếu không có IconButton thì không thêm
            tileColor: Colors.transparent,
            selected: selectedOption == i,
          ),
        ),
      );
    }

    return Column(
      children: optionWidgets,
    );
  }



  Widget slider() {
    return BrightnessWidget(
      progressVal: progressVal,
      color: activeColor[progressVal],
      onChange: (value) {
        setState(() {
          percent = value;
          progressVal = normalize(value, kMinDegree, kMaxDegree);
        });
      },
    );
  }

  Widget controls() {
    return Column(
      children: [
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Expanded(
              child: SpeedWidget(
                  speed: speed,
                  changeSpeed: (val) => setState(() {
                    speed = val;
                  })),
            ),
            const SizedBox(
              width: 15,
            ),
            Expanded(
              child: PowerWidget(
                  isActive: isActive,
                  onChanged: (val) => setState(() {
                    isActive = val;
                  })),
            ),
          ],
        ),
        const SizedBox(
          height: 15,
        ),
        PercentWidget(
          percent: percent,
          changePercent: (val) {
            setState(() {
              percent = val;
              progressVal = normalize(val, kMinDegree, kMaxDegree);
            });
            // Gửi tin nhắn cho WebSocket
            // _webSocket.send(deviceIP, {"type": "E","value": 3});
            _webSocket.send(deviceIP, {"type": "B","value": val});
          },
        ),
        const SizedBox(
          height: 15,
        ),
      ],
    );
  }
}
