# کد های پنجره (Win Form) ویندوز

### فشرده شدن کلید هرکجای پنجره

``` csharp
protected override bool ProcessCmdKey(ref Message msg, Keys keyData)
{
    if (keyData == (Keys.Control | Keys.Shift | Keys.Alt | Keys.X))
    {
        // do some
        return true;
    }
    return base.ProcessCmdKey(ref msg, keyData);
}
```

### حرکت موس هرکجای صفحه

``` csharp
{
    private void Form1_Load(object sender, EventArgs e)
    {
        GlobalMouseHandler gmh = new GlobalMouseHandler();
        gmh.TheMouseMoved += new MouseMovedEvent(gmh_TheMouseMoved);
        Application.AddMessageFilter(gmh);
    }
    private void gmh_TheMouseMoved()
    {
        Point cur_pos = System.Windows.Forms.Cursor.Position;
        this.Text = cur_pos.X + ":" + cur_pos.Y;
    }
}
public delegate void MouseMovedEvent();
public class GlobalMouseHandler : IMessageFilter
{
    private const int WM_MOUSEMOVE = 0x0200;
    public event MouseMovedEvent TheMouseMoved;
    public bool PreFilterMessage(ref Message m)
    {
        if (m.Msg == WM_MOUSEMOVE)
        {
            if (TheMouseMoved != null)
            {
                TheMouseMoved();
            }
        }
        return false; // Always allow message to continue to the next filter control
    }
}
```

### حرکت گوی موس هرکجای صفحه

``` csharp
protected override void OnMouseWheel(MouseEventArgs e)
{
    Point pt_MouseAbs = Control.MousePosition;
    Control i_Ctrl = dataGridView1;
    do
    {
        Rectangle r_Ctrl = i_Ctrl.RectangleToScreen(i_Ctrl.ClientRectangle);
        if (!r_Ctrl.Contains(pt_MouseAbs))
        {
            base.OnMouseWheel(e);
            return; // mouse position is outside the picturebox or it's parents
        }
        string text = "up";
        if (e.Delta <= 0) text = "down";
        MessageBox.Show(text);
        i_Ctrl = i_Ctrl.Parent;
    }
    while (i_Ctrl != null && i_Ctrl != this);
}
```

### کشیدن و رها کردن (Drag and drop)

``` csharp
public Form1()
{
    InitializeComponent();
    this.AllowDrop = true;
    this.DragEnter += new DragEventHandler(Form1_DragEnter);
    this.DragDrop += new DragEventHandler(Form1_DragDrop);
}
void Form1_DragEnter(object sender, DragEventArgs e)
{
    if (e.Data.GetDataPresent(DataFormats.FileDrop)) e.Effect = DragDropEffects.Copy;
}
void Form1_DragDrop(object sender, DragEventArgs e)
{
    textBox1.Text = "";
    string[] files = (string[])e.Data.GetData(DataFormats.FileDrop);
    textBox1.Text = System.IO.File.ReadAllText(files[0]);
    textBox1.Text += "\r\n**********************\r\n";
    foreach (string file in files) textBox1.Text += file + Environment.NewLine;
}
```

### گرفتن نمایه فایل

``` csharp
Icon.ExtractAssociatedIcon(open.FileName).ToBitmap();
```