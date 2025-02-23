# This is Summary in C#
```csharp
/// <summary>
/// Calculates the tax for a given amount.
/// </summary>
/// <param name="amount">The amount to calculate tax on.</param>
/// <param name="rate">The tax rate as a percentage.</param>
/// <returns>The calculated tax amount.</returns>
public decimal CalculateTax(decimal amount, decimal rate)
{
    return amount * (rate / 100);
}
```

# Working with Lists
```csharp
using System;
using System.Collections.Generic;

List<string> names = new List<string> { "Alice", "Bob", "Charlie" };
names.ForEach(name => Console.WriteLine(name));
```

# Working with File Stream
## 1. Các lớp chính trong System.IO

Lớp	Chức năng chính
- File: Các thao tác với tệp tin (tạo, đọc, ghi, xóa, sao chép, di chuyển, kiểm tra tồn tại).
- FileInfo:	Cung cấp thông tin về tệp tin và thực hiện các thao tác tương tự File.
- Directory: Quản lý thư mục (tạo, xóa, lấy danh sách tệp/thư mục con, kiểm tra tồn tại).
- DirectoryInfo: Cung cấp thông tin và thao tác trên thư mục giống Directory.
- Path: Xử lý đường dẫn tệp/thư mục (ghép chuỗi, trích xuất tên, phần mở rộng, thư mục cha).
- StreamReader: Đọc nội dung tệp theo dòng hoặc toàn bộ tệp.
- StreamWriter: Ghi dữ liệu vào tệp.
- FileStream: Đọc/Ghi dữ liệu nhị phân từ tệp.
## 2. Kiểm tra sự tồn tại của tệp/thư mục
### a. Kiểm tra tệp có tồn tại không?
```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string filePath = "test.txt";

        if (File.Exists(filePath))
            Console.WriteLine("Tệp tồn tại.");
        else
            Console.WriteLine("Tệp không tồn tại.");
    }
}

```
### b. Kiểm tra thư mục có tồn tại không?
```csharp
string folderPath = @"C:\MyFolder";

if (Directory.Exists(folderPath))
    Console.WriteLine("Thư mục tồn tại.");
else
    Console.WriteLine("Thư mục không tồn tại.");

```
## 3. Tạo tệp & thư mục
### a. Tạo một tệp rỗng
``` csharp
string filePath = "newfile.txt";
File.Create(filePath).Close(); // Đóng ngay để tránh lỗi khóa file
```
### b. Tạo thư mục
``` csharp
string folderPath = "NewFolder";
Directory.CreateDirectory(folderPath);
```
## 4. Stream-based File Handling

### a. Read a File Line by Line
```csharp
using (StreamReader reader = new StreamReader("example.txt"))
{
    string line;
    while ((line = reader.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

### b. Write Multiple Lines to a File
```csharp
using (StreamWriter writer = new StreamWriter("example.txt", append: true))
{
    writer.WriteLine("Line 1");
    writer.WriteLine("Line 2");
}
```

## 5. Copy, Move, and Delete Files and Directories

### a. Copy a File
```csharp
File.Copy("example.txt", "copy_example.txt", overwrite: true);
```

### b. Move a File
```csharp
File.Move("copy_example.txt", @"C:\\NewFolder\\moved_example.txt");
```

### c. Delete a File
```csharp
File.Delete("example.txt");
```

### d. Delete a Directory
```csharp
Directory.Delete("NewFolder", recursive: true);
```

## 6. List Files and Directories

### a. Get All Files in a Directory
```csharp
string folderPath = @"C:\\MyFolder";
string[] files = Directory.GetFiles(folderPath);

foreach (var file in files)
    Console.WriteLine(file);
```

### b. Get All Subdirectories
```csharp
string[] directories = Directory.GetDirectories(folderPath);

foreach (var dir in directories)
    Console.WriteLine(dir);
```

## 7. Binary File Operations with FileStream

### a. Read a Binary File
```csharp
using (FileStream fs = new FileStream("image.jpg", FileMode.Open, FileAccess.Read))
{
    byte[] buffer = new byte[fs.Length];
    fs.Read(buffer, 0, buffer.Length);
    Console.WriteLine("Binary file read successfully.");
}
```

### b. Write a Binary File
```csharp
byte[] data = { 1, 2, 3, 4, 5 };

using (FileStream fs = new FileStream("binary.dat", FileMode.Create, FileAccess.Write))
{
    fs.Write(data, 0, data.Length);
}
```

## 8. Exception Handling in File System Operations
```csharp
try
{
    string content = File.ReadAllText("nonexistent.txt");
    Console.WriteLine(content);
}
catch (FileNotFoundException ex)
{
    Console.WriteLine("Error: File not found.");
}
catch (UnauthorizedAccessException ex)
{
    Console.WriteLine("Error: Access denied.");
}
catch (Exception ex)
{
    Console.WriteLine($"Unexpected error: {ex.Message}");
}
```

## 9. Monitor File System Changes with FileSystemWatcher
```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        FileSystemWatcher watcher = new FileSystemWatcher(@"C:\\MyFolder");
        watcher.NotifyFilter = NotifyFilters.FileName | NotifyFilters.LastWrite;

        watcher.Changed += (sender, e) => Console.WriteLine($"File {e.Name} changed.");
        watcher.Created += (sender, e) => Console.WriteLine($"File {e.Name} created.");
        watcher.Deleted += (sender, e) => Console.WriteLine($"File {e.Name} deleted.");
        watcher.Renamed += (sender, e) => Console.WriteLine($"File {e.OldName} renamed to {e.Name}.");

        watcher.EnableRaisingEvents = true;

        Console.WriteLine("Monitoring directory changes. Press Enter to exit.");
        Console.ReadLine();
    }
}
```

# EventHandler
```csharp
using System;

namespace CustomEventHandler
{
    // Define a custom class to hold event data
    public class CustomEventArgs : EventArgs
    {
        public string Message { get; }
        public CustomEventArgs(string message) => Message = message;
    }

    // Publisher class
    public class Publisher
    {
        // Declare an event
        public event EventHandler<CustomEventArgs> OnPublish;

        public void Publish(string message)
        {
            OnPublish?.Invoke(this, new CustomEventArgs(message));
        }
    }

    // Subscriber class
    public class Subscriber
    {
        public void Subscribe(Publisher pub)
        {
            pub.OnPublish += HandleEvent;
        }

        private void HandleEvent(object sender, CustomEventArgs e)
        {
            Console.WriteLine($"Received message: {e.Message}");
        }
    }

    class Program
    {
        static void Main()
        {
            var publisher = new Publisher();
            var subscriber = new Subscriber();

            subscriber.Subscribe(publisher);
            publisher.OnPublish += (sender, e) => {
                Console.WriteLine($"Also received message: {e.Message}");
            }
            publisher.Publish("Hello, Events in C#!");
        }
    }
}

```

# Sử dụng NetworkStream C#
## 1. WinForms TCP Server (Multi-Client, ASCII Encoding)
``` csharp
using System;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Threading;
using System.Windows.Forms;

public partial class ServerForm : Form
{
    private TcpListener server;
    private Thread serverThread;
    private bool isRunning = false;

    public ServerForm()
    {
        InitializeComponent();
        CheckForIllegalCrossThreadCalls = false; // Allow UI updates from other threads
    }

    private void btnStart_Click(object sender, EventArgs e)
    {
        isRunning = true;
        serverThread = new Thread(StartServer);
        serverThread.IsBackground = true;
        serverThread.Start();
        txtLog.AppendText("🚀 Server started on port 5000...\r\n");
    }

    private void StartServer()
    {
        try
        {
            server = new TcpListener(IPAddress.Any, 5000);
            server.Start();

            while (isRunning)
            {
                if (!server.Pending()) // Prevent CPU overload
                {
                    Thread.Sleep(100);
                    continue;
                }

                TcpClient client = server.AcceptTcpClient();
                Thread clientThread = new Thread(() => HandleClient(client));
                clientThread.IsBackground = true;
                clientThread.Start();
            }
        }
        catch (Exception ex)
        {
            txtLog.Invoke((MethodInvoker)(() => txtLog.AppendText("❌ Server error: " + ex.Message + "\r\n")));
        }
    }

    private void HandleClient(TcpClient client)
    {
        try
        {
            NetworkStream stream = client.GetStream();
            byte[] buffer = new byte[1024];

            while (client.Connected)
            {
                int bytesRead = stream.Read(buffer, 0, buffer.Length);
                if (bytesRead == 0) break; // Client disconnected

                string message = Encoding.ASCII.GetString(buffer, 0, bytesRead);
                txtLog.Invoke((MethodInvoker)(() => txtLog.AppendText("📩 Client: " + message + "\r\n")));

                // Send response
                string response = "Server received: " + message;
                byte[] responseBytes = Encoding.ASCII.GetBytes(response);
                stream.Write(responseBytes, 0, responseBytes.Length);
            }
        }
        catch (Exception ex)
        {
            txtLog.Invoke((MethodInvoker)(() => txtLog.AppendText("❌ Client error: " + ex.Message + "\r\n")));
        }
        finally
        {
            client.Close();
            txtLog.Invoke((MethodInvoker)(() => txtLog.AppendText("❌ Client disconnected.\r\n")));
        }
    }

    private void btnStop_Click(object sender, EventArgs e)
    {
        isRunning = false;
        server?.Stop();
        txtLog.AppendText("🛑 Server stopped.\r\n");
    }
}

```
## 2. WinForms TCP Server (Multi-Client, ASCII Encoding)

``` csharp
using System;
using System.Net.Sockets;
using System.Text;
using System.Threading;
using System.Windows.Forms;

public partial class ClientForm : Form
{
    private TcpClient client;
    private NetworkStream stream;
    private Thread readThread;
    private bool isRunning = false;

    public ClientForm()
    {
        InitializeComponent();
    }

    private void btnConnect_Click(object sender, EventArgs e)
    {
        ConnectToServer();
    }

    private void ConnectToServer()
    {
        try
        {
            client = new TcpClient("127.0.0.1", 5000);
            stream = client.GetStream();
            txtLog.AppendText("✅ Connected to server.\r\n");

            isRunning = true;
            readThread = new Thread(ReadMessages);
            readThread.IsBackground = true;
            readThread.Start();
        }
        catch (Exception ex)
        {
            txtLog.AppendText("❌ Connection failed: " + ex.Message + "\r\n");
        }
    }

    private void btnSend_Click(object sender, EventArgs e)
    {
        if (client == null || !client.Connected)
        {
            txtLog.AppendText("❌ Not connected to server.\r\n");
            return;
        }

        try
        {
            string message = txtMessage.Text.Trim();
            if (string.IsNullOrEmpty(message))
            {
                txtLog.AppendText("⚠️ Cannot send an empty message.\r\n");
                return;
            }

            // Send message
            byte[] data = Encoding.ASCII.GetBytes(message);
            stream.Write(data, 0, data.Length);
            txtLog.AppendText("📤 Sent: " + message + "\r\n");
        }
        catch (Exception ex)
        {
            txtLog.AppendText("❌ Error sending message: " + ex.Message + "\r\n");
        }
    }

    private void ReadMessages()
    {
        try
        {
            byte[] buffer = new byte[1024];
            while (isRunning)
            {
                int bytesRead = stream.Read(buffer, 0, buffer.Length);
                if (bytesRead == 0) break; // Server disconnected

                string response = Encoding.ASCII.GetString(buffer, 0, bytesRead);
                txtLog.Invoke((MethodInvoker)(() => txtLog.AppendText("📩 Server: " + response + "\r\n")));
            }
        }
        catch (Exception ex)
        {
            txtLog.Invoke((MethodInvoker)(() => txtLog.AppendText("❌ Error receiving message: " + ex.Message + "\r\n")));
        }
    }

    private void btnDisconnect_Click(object sender, EventArgs e)
    {
        isRunning = false;
        client?.Close();
        txtLog.AppendText("❌ Disconnected from server.\r\n");
    }
}

```

# CÁC CÂU LỆNH SQL CƠ BẢN
## 1. Chèn dữ liệu (INSERT)
```sql
INSERT INTO Customers (CustomerID, Name, Email, Age)
VALUES (1, 'Nguyễn Văn A', 'a@abc.com', 25)

```
## 2. Truy vấn dữ liệu (SELECT)
```sql
SELECT * FROM Customers;
SELECT Name, Age FROM Customers WHERE Age > 18;

```
## 3. Cập nhật dữ liệu (UPDATE)
```sql
Update Customers
SET Age = 30
Where CustomerID = 1;

```
## 4. Xóa dữ liệu (DELETE)
```sql
DELETE FROM Customers WHERE CustomerID = 1

```

## 5. Ràng buộc dữ liệu
- Primary key: Định danh duy nhất từng dòng
- Foreign key: Liên kết bảng khác (khóa ngoại)
- UNIQUE: Đảm bảo giá trị không trùng lặp
- NOT NULL: Bắt buộc có giá trị
- CHECK: Giới hạn giá trị
```sql
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    OrderDate DATE,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);
```
## 6. Extra Code database

```Csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Data.SqlClient;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace ConnectDatabase
{
    public partial class Form1 : Form
    {
        private readonly string _ConnectionString = "Server = HUYNGUYEN\\TONYNGUYEN; database = NhanVien; User = sa; password = Nchbka1999";
        private SqlConnection _sqlConnection;
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {

        }

        private void btnConnect_Click(object sender, EventArgs e)
        {
            try
            {
                if(_sqlConnection == null)
                {
                    _sqlConnection = new SqlConnection(_ConnectionString);
                    _sqlConnection.Open();
                    lblConnect.Text = "Connected!";
                    lblConnect.ForeColor = Color.Green;
                    btnConnect.Text = "Disconnect";
                    MessageBox.Show("Connected to Server");
                }
                else
                {
                    _sqlConnection?.Close();
                    lblConnect.Text = "Disconnect";
                    lblConnect.ForeColor = Color.Red;
                    btnConnect.Text = "Connect";
                    MessageBox.Show("Disconnected!");

                }

            }
            catch(Exception ex) {
                MessageBox.Show(ex.ToString());
            }
        }

        private void InsertDataIntoDatabase(string _maNhanVien, string _hoTen, DateTime _ngaySinh, string _diaChi, string _phongBan, int _luongCoBan)
        {
            string query = "INSERT INTO NhanVien (MaNhanVien, HoTen, NgaySinh, DiaChi, PhongBan, LuongCoBan) VALUES (@maNhanVien, @hoTen, @ngaySinh, @diaChi, @phongBan, @luongCoBan)";
           // "@maNhanVien, @hoTen, @ngaySinh, @diaChi, @phongBan, @luongCoBan"
            SqlCommand command = new SqlCommand(query, _sqlConnection);
            command.Parameters.AddWithValue("@maNhanVien", _maNhanVien);
            command.Parameters.AddWithValue("@hoTen", _hoTen);
            command.Parameters.AddWithValue("@ngaySinh", _ngaySinh);
            command.Parameters.AddWithValue("@diaChi", _diaChi);
            command.Parameters.AddWithValue("@phongBan", _phongBan);
            command.Parameters.AddWithValue("@luongCoBan", _luongCoBan);
            try
            {
                command.ExecuteNonQuery();
                Console.WriteLine("Inserted data to Database");
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message.ToString());
            }
        }

        private void btnInsert_Click(object sender, EventArgs e)
        {
            DateTime x; DateTime.TryParse("2024-02-09 14:30", out x);
            InsertDataIntoDatabase("a23541555", "Nguyen Quoc Dat", x, "Ha Noi", "Ke toan", 7000000);
            MessageBox.Show("Insert successful!");
        }

        private void btnLoadData_Click(object sender, EventArgs e)
        {
            LoadData();
        }
        private void LoadData()
        {
            string connectionString = "Server=HUYNGUYEN\\TONYNGUYEN;Database=NhanVien;user = sa; password = Nchbka1999";
            string query = "SELECT * FROM NhanVien";  // Replace 'YourTable' with the actual table name

            using (SqlConnection conn = new SqlConnection(connectionString))
            {
                SqlDataAdapter adapter = new SqlDataAdapter(query, conn);
                DataTable dt = new DataTable();
                adapter.Fill(dt);
                dataGridView1.DataSource = dt; // Bind data to DataGridView
            }
        }

        private void btnDelete_Click(object sender, EventArgs e)
        {

        }
        //private void 
    }
}

```

# RS485 XINJE
```Csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Diagnostics.Tracing;
using System.Drawing;
using System.IO.Ports;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Windows.Forms.VisualStyles;

namespace PCToXinjeRS485
{
    public partial class Form1 : Form
    {
        public static string _message;
        public static byte[] request = new byte[] { 0x01, 0x03, 0x00, 0x00, 0x00, 0x0A, 0xC5, 0xCD };
        public static byte[] request2 = new byte[] { 0x01, 0x03, 0x00, 0x00, 0x00, 0x01, 0xC5, 0xCD };
        public Form1()
        {
            InitializeComponent();
            

            serialPort1.DataReceived += SerialPort1_DataReceived;
        }

        private void Form1_Load(object sender, EventArgs e)
        {



        }

        private void SerialPort1_DataReceived(object sender, SerialDataReceivedEventArgs e)
        {
            //byte[] _temp = serialPort1.ReadExisting();

            //Invoke(new Action(() =>
            //{
            //    textBoxData.Text = _temp.ToString();
            //}));


            int bytesToRead = serialPort1.BytesToRead;
            byte[] buffer = new byte[bytesToRead];
            
            //if (bytesToRead >= 25)
            //{
            //    receivedData = Encoding.ASCII.GetString(buffer);

            //    Invoke(new Action(() =>
            //    {
            //        lbD0Value.Text = receivedData;
            //    }));
            //}
            if (bytesToRead == 25)
            {
                serialPort1.Read(buffer, 0, bytesToRead);
                Invoke(new Action(() =>
                {
                    textBoxData.AppendText(BitConverter.ToString(buffer) + Environment.NewLine);
                }));

                string temp = textBoxData.Text.ToString();
                try
                {
                    string[] tmp = temp.Split('-');
                    string hexString1 = "0x" + tmp[3] + tmp[4];

                    int intValue1 = Convert.ToInt32(hexString1.Replace("0x", ""), 16); // Remove '0x' and parse as base 16

                    Invoke(new Action(() =>
                    {
                        lbD0Value.Text = intValue1.ToString();
                    }));
                }
                catch { }
            }

            // Hiển thị dữ liệu nhận được


            //Thread.Sleep(10);



        }

        private void btnConnect_Click(object sender, EventArgs e)
        {
            try
            {
                serialPort1.PortName = cbCOM.Text.ToString();
                int.TryParse(cbBaurates.Text.ToString(), out int x);
                serialPort1.BaudRate = x;
                int.TryParse(cbDataBits.Text.ToString(), out int y);
                serialPort1.DataBits = y;
                var c1 = serialPort1.Parity.ToString();
                try
                {
                    switch (c1)
                    {
                        case "None":
                            serialPort1.Parity = Parity.None;
                            break;
                        case "Odd":
                            serialPort1.Parity = Parity.Odd;
                            break;
                        case "Even":
                            serialPort1.Parity = Parity.Even;
                            break;
                    }
                    var c2 = serialPort1.StopBits.ToString();
                    switch (c2)
                    {
                        case "One":
                            serialPort1.StopBits = StopBits.One;
                            break;
                        case "Two":
                            serialPort1.StopBits = StopBits.Two;
                            break;
                    }
                }
                catch
                {
                    serialPort1.StopBits = StopBits.One;
                    serialPort1.Parity = Parity.None;
                }
            }
            catch(Exception ex)
            {
                MessageBox.Show($"Lỗi kết nối: {ex.Message}");
                this.Close(); }
            
            

            try
            {
                if (!serialPort1.IsOpen)
                {
                    serialPort1.Open();
                    lblStatus.Text = "Connected!";
                    lblStatus.ForeColor = Color.Green;
                    MessageBox.Show($"{cbCOM.Text.ToString()} Kết nối thành công!");
                }
                
            }
            catch (Exception ex)
            {
                MessageBox.Show($"Lỗi kết nối: {ex.Message}");
            }
        }

        private static ushort CalculateCRC(byte[] data, int length)
        {
            ushort crc = 0xFFFF;
            for (int i = 0; i < length; i++)
            {
                crc ^= data[i];
                for (int j = 0; j < 8; j++)
                {
                    if ((crc & 0x01) != 0)
                        crc = (ushort)((crc >> 1) ^ 0xA001);
                    else
                        crc >>= 1;
                }
            }
            return crc;
        }

        private void btnDisconnect_Click(object sender, EventArgs e)
        {
            if (serialPort1.IsOpen)
            {
                timer1.Enabled = false;
                serialPort1.Close();
                lblStatus.Text = "Disconnect!";
                lblStatus.ForeColor = Color.Red;
                MessageBox.Show("Đã ngắt kết nối!");
            }
        }

        private void btnSend_Click(object sender, EventArgs e)
        {
            if (serialPort1.IsOpen)
            {
                if (textBoxData.Text != "")
                {
                    Invoke(new Action(() =>
                    {
                        textBoxData.Text = "";
                    }));
                }

                //serialPort1.Write(request, 0, request.Length);
                timer1.Enabled = true;
                MessageBox.Show("Request sent!");
            }
            else
            {
                MessageBox.Show("No connection!");
            }
        }

        private void timer1_Tick(object sender, EventArgs e)
        {
            if (textBoxData.Text != "")
            {
                Invoke(new Action(() =>
                {
                    textBoxData.Text = "";
                }));
            }
            serialPort1.Write(request, 0, request.Length);

        }
    }
}

```

# TCP-Modbus
```Csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Windows.Forms;
using NModbus;
using EasyModbus;
namespace TCP_IP_RS485
{
    public partial class Form1 : Form
    {
        private TcpListener server;
        private Thread listenerThread;
        private bool isRunning;
        string ipAddress = string.Empty;
        ModbusServer modbusServer;
        ModbusClient modbusClient;
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            ipAddress = txtIPAddress.Text;
        }

        private void btnStartServer_Click(object sender, EventArgs e)
        {
            IPAddress ipAddress = IPAddress.Parse(txtIPAddress.Text.ToString());
            int port = 6432;

            server = new TcpListener(ipAddress, port);
            server.Start();
            isRunning = true;

            listenerThread = new Thread(ListenForClients);
            listenerThread.Start();

            listBoxLogs.Items.Add($"Server started at {ipAddress}:{port}");
            btnStartServer.Enabled = false;
        }

        private void ListenForClients()
        {
            while (isRunning)
            {
                try
                {
                    TcpClient client = server.AcceptTcpClient();
                    listBoxLogs.Invoke((Action)(() =>
                        listBoxLogs.Items.Add($"Client connected: {client.Client.RemoteEndPoint}")
                    ));

                    Thread clientThread = new Thread(HandleClientCommunication);
                    clientThread.Start(client);
                }
                catch (Exception ex)
                {
                    listBoxLogs.Invoke((Action)(() =>
                        listBoxLogs.Items.Add($"Error: {ex.Message}")
                    ));
                }
            }
        }

        private void HandleClientCommunication(object clientObj)
        {
            TcpClient client = (TcpClient)clientObj;
            NetworkStream stream = client.GetStream();

            byte[] buffer = new byte[1024];
            int bytesRead;

            while ((bytesRead = stream.Read(buffer, 0, buffer.Length)) > 0)
            {
                string message = Encoding.ASCII.GetString(buffer, 0, bytesRead);
                listBoxLogs.Invoke((Action)(() =>
                    listBoxLogs.Items.Add($"Received: {message}")
                ));

                // Echo the message back to the client
                byte[] response = Encoding.ASCII.GetBytes($"Echo: {message}");
                stream.Write(response, 0, response.Length);
            }

            client.Close();
            listBoxLogs.Invoke((Action)(() =>
                listBoxLogs.Items.Add("Client disconnected.")
            ));
        }
    }
    
}

```

# INDEXERS
```Csharp
using System;

class MultiFieldIndexer
{
    private int[] evenNumbers = new int[5]; // One array for even indices
    private int[] oddNumbers = new int[5];  // Another array for odd indices

    public int this[int index]
    {
        get
        {
            if (index % 2 == 0)
                return evenNumbers[index / 2]; // Access evenNumbers
            else
                return oddNumbers[index / 2]; // Access oddNumbers
        }
        set
        {
            if (index % 2 == 0)
                evenNumbers[index / 2] = value; // Modify evenNumbers
            else
                oddNumbers[index / 2] = value; // Modify oddNumbers
        }
    }
}

class Prog
{
    public static void Main(string[] args)
    {
        MultiFieldIndexer myArray = new MultiFieldIndexer();
        myArray[0] = 22;
        myArray[1] = 45; myArray[2] = 28;
        Console.WriteLine(myArray[0]);
        Console.WriteLine(myArray[1]);
        Console.WriteLine(myArray[2]);
        Console.WriteLine(myArray[3]);
        Console.WriteLine(myArray[4]);
        Console.WriteLine(myArray[5]);
        Console.WriteLine(myArray[6]);
        Console.WriteLine(myArray[7]);
        Console.WriteLine(myArray[8]);
        Console.WriteLine(myArray[9]);
    }
}
```

