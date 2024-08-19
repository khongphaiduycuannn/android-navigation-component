# Navigation Component
## Tổng quan
Navigation Component là một thư viện giúp người dùng quản lí các điều hướng giữa các màn hình trong ứng dụng.

### Các khái niệm chính
<table>
    <tr>
        <th><p>Khái niệm</p></th>
        <th><p>Chức năng</p></th>
    </tr>
        <tr>
	        <td>Destination (Đích đến)</td>
	        <td>Là một thành phần giao diện như `Fragment`, `Activity`, `Dialog`, ...</td>
	    </tr>
    <tr>
        <td>Host</td>
        <td>Chứa destination hiện tại và hiển thị lên màn hình.</td>
    </tr>
    <tr>
        <td>Graph</td>
        <td>Xác định tất cả destination điều hướng trong ứng dụng và cách kết nối các destination này với nhau.</td>
    </tr>
    <tr>
        <td>Controller</td>
        <td>Cung cấp các phương thức để điều hướng giữa các destination, quản lý back stack, ...</td>
    </tr>
</table>

### Cài đặt
Trong file app `build gradle`:

	dependencies {  
		val nav_version =  "2.7.7"  
	
		// Kotlin 
		implementation("androidx.navigation:navigation-fragment-ktx:$nav_version") 
		implementation("androidx.navigation:navigation-ui-ktx:$nav_version")   
	}

## Navigation Host
Chứa destination hiện tại (Fragment, Activity, Dialog) và hiển thị chúng lên màn hình.
Trong file xml giao diện, sử dụng một `FragmentContainerView` làm Nav Host:

	<androidx.fragment.app.FragmentContainerView  
	  android:id="@+id/navHostFragment"
	  android:name="androidx.navigation.fragment.NavHostFragment"  
	  android:layout_width="match_parent"  
	  android:layout_height="match_parent"  
	  app:defaultNavHost="true"  
	  app:navGraph="@navigation/main_nav" />

## Navigation Graph
Chịu trách nhiệm xác định tất cả destination điều hướng trong ứng dụng và cách kết nối các destination này với nhau.

### Tổng quan
Có thể xây dựng trực tiếp Navigation Graph bằng mã XML.

![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/add%20nav%20graph.png)

Sau khi tạo cần liên kết Nav Graph vừa tạo với Nav Host bằng thuộc tính:

	<androidx.fragment.app.FragmentContainerView
		...
		app:navGraph="@navigation/main_nav" />

Sau khi tạo XML, Android Studio sẽ hiển thị giao diện chỉnh sửa nav graph:
![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/nav%20graph%20gui.png)

### Thêm destination
Chọn biểu tượng **New Destination** -> **Create new destination** -> Chọn destination.
![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/add%20destination.png)

Destination mới được ra kèm theo thuộc tính `id`, được sử dụng để tham chiếu, di chuyển đến destination trong mã.

### Kết nối các destination
1. Trong giao diện **design**, trỏ con trỏ chuột ở destination ban đầu, một vòng tròn sẽ xuất hiện phía bên phải của destination.

![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/add%20action.png)

2. Kéo con trỏ đến destination đích rồi thả ra.

![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/add%20action%20success.png)

Lúc này bên gai diện **Code**, một thẻ `action` được sinh ra trên destination tương ứng. Nó chịu trách nhiệm điều hướng giữa 2 destination được kết nối trong mã.

![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/action%20in%20code.png)

3. Nhấp chuột vào mũi tên bên giao diện **design** để điều chỉnh các thuộc tính của `action` .

![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/action%20attributes.png)

## Navigation Controller
Trong fragment hoặc view, để lấy ra NavController.

	val navController = findNavController()

`Nav Controller` sử dụng một back stack để chứa các destination mà người dùng truy cập. Khi thêm một destination, nó sẽ thêm vào đầu stack, thành phần ở đỉnh stack sẽ hiển thị cho người dùng.

### Điều hướng
Sử dụng hàm `NavController.navigate()` để điều hướng giữa các destination.
Có nhiều phương thức nạp chồng cho `navigate()`, ta có thể chọn phương thức phù hợp tùy vào hoàn cảnh cụ thể.

	navController.navigate(R.id.destinationId) // Id destination

hoặc

	navController.navigate(R.id.actionId) // Id action

Sau khi gọi hàm `navigate()` đến một destination nào đó, nó sẽ được thêm vào đỉnh back stack và hiển thị cho người dùng.

Để loại bỏ destination ở đỉnh và quay trở lại destination trước đó ta sử dụng các hàm `popBackStack()` hoặc `navigateUp()`.

### Truyền dữ liệu giữa các destination
#### Truyền dữ liệu sử dụng Bundle

Tạo một đối tượng `Bundle` và truyền đối tượng đó đến đích bằng cách sử dụng `navigate()`, như trong ví dụ sau:

	val bundle = bundleOf("name" to "Quan")  
	view.findNavController().navigate(R.id.action_id, bundle)

Trong mã của destination, lấy bundle nhận được qua thuộc tính `arguments`:

	val tv = view.findViewById<TextView>(R.id.tvName)  
	tv.text = arguments?.getString("name")

#### Truyền dữ liệu sử dụng Safe Args
**Safe Args** là một plugin Gradle được tích hợp với Navigation Component, giúp việc truyền dữ liệu giữa các Fragment và Activity trở nên an toàn và hiệu quả hơn.

Sau khi khai báo trước kiểu dữ liệu của các đối số truyền vào, Safe Args sẽ tự động tạo ra các lớp đối tượng giúp ta truy cập vào các đối số một cách dễ dàng. Ta không cần phải viết thủ công các đoạn code để parse dữ liệu từ Bundle và giảm thiểu các lỗi do viết code thủ công gây ra.

##### Cài đặt
Trong file project `build.gradle`:

	buildscript { 
		repositories { 
			google()  
		} 

		dependencies {  
			val nav_version =  "2.7.7"
			classpath("androidx.navigation:navigation-safe-args-gradle-plugin:$nav_version")  }  
	}

Trong file app `build.gradle`, thêm plugin:

	plugins { 
		id("androidx.navigation.safeargs.kotlin")  
	}

##### Truyền dữ liệu
1. Trong giao diện **design**, nhấp chuột vào destination đích của `action` .
2. Trong phần **Arguments** của bảng **Attributes**, bấm nút **Add Argument** để thêm thuộc tính cần chuyển.

![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/add%20destination%20attribute.png)

Sau khi bật Safe Args, một số lớp được tạo ra cho các destination và `action` tương ứng:

1. Một lớp được tạo cho mỗi destination nơi bắt nguồn một `action`. Tên của lớp này là tên của destination ban đầu, nối thêm từ "Directions" (nếu đích xuất phát là một Fragment có tên là `FirstFragment`, thì lớp được tạo sẽ gọi là `FirstFragmentDirections`).

2.   Đối với từng `action` dùng để truyền đối số, một lớp bên trong được tạo sẽ có tên dựa trên thao tác đó (nếu thao tác gọi là  `action_firstFragment_to_secondFragment`, thì lớp được đặt tên là  `ActionFirstFragmentToSecondFragmentAction`).

3.   Một lớp được tạo cho destination đích. Tên của lớp này là tên của đích, nối thêm từ "Args" (nếu Fragment đích có tên  `SecondFragment`, thì lớp được tạo sẽ gọi là  `SecondFragmentArgs`.

Ví dụ sau đây cho thấy cách sử dụng những lớp này để đặt một đối số và truyền đối số đó đến phương thức `navigate()`:

- Trong destination bắt đầu:

  	override  fun onClick(v:  View)  {
  		val action = FirstFragmentDirections.actionFirstFragmentToSecondFragmentAction("Quan")
  		v.findNavController().navigate(action)  
  	}

- Trong destination đích:

  	val args:  ConfirmationFragmentArgs by navArgs()  

  	override  fun onViewCreated(
  		view:  View, 
  		savedInstanceState: Bundle?
  	)  { 
  	println(args.name)
  }

### Tạo hiệu ứng chuyển tiếp giữa các đích đến
1. Trong giao diện **design**, chọn mũi tên `action` giữa 2 đích đến.
2. Trong phần **Animations** của bảng **Attributes**, nhấp vào mũi tên thả xuống để hiển thị các loại hiệu ứng:

![no description](https://github.com/khongphaiduycuannn/android-navigation-component/blob/main/action%20animation.png)

3. Tạo file XML hiệu ứng và điền vào các trường thích hợp.
