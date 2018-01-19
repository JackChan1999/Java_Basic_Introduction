## JFrame

窗口

| 方法声明                       | 功能描述                                     |
| -------------------------- | ---------------------------------------- |
| setSize()                  |                                          |
| setPreferredSize()         |                                          |
| setResizable()             |                                          |
| setDefaultCloseOperation() |                                          |
| setVisible()               |                                          |
| setContentPane()           |                                          |
| pack()                     | 在setResizable(false)后进行pack()，防止在Windows下系统修改frame的尺寸 |
| repaint()                  |                                          |
| addKeyListener()           |                                          |
| addMouseListener()         |                                          |

## JPanel

面板

| 方法声明               | 功能描述 |
| ------------------ | ---- |
| paintComponent()   |      |
| getPreferredSize() |      |

## Graphics

| 方法声明       | 功能描述 |
| ---------- | ---- |
| drawOval() |      |

### Graphics2D

| 方法声明                | 功能描述 |
| ------------------- | ---- |
| setStroke()         |      |
| setColor()          |      |
| draw()              |      |
| fill()              |      |
| addRenderingHints() |      |
| drawImage()         |      |
| drawString()        |      |
| getFontMetrics()    |      |

- Ellipse2D
- BasicStroke
- RenderingHints 抗锯齿
  - put()
- FontMetrics
- ImageIcon

## Dimension

## KeyAdapter

键盘事件监听

| 方法声明        | 功能描述 |
| ----------- | ---- |
| keyReleased |      |

- KeyEvent

## MouseAdapter

鼠标事件监听

## EventQueue

- invokeLater()