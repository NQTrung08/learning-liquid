# LIQUID

## Basics

1. Giới thiệu
2. Toán tử
   1. các toán tử  `and` và `or` được đánh giá từ `phải sang trái`

      1. ```liquid
            {% if true and false and false or true %}
               This evaluates to false, since the tags are checked like this:

               true and (false and (false or true))
               true and (false and true)
               true and false
               false
            {% endif %}
         
         ```

3. Truthy and falsy
   1. Tất cả giá trị trong Liquid đều `true` ngoại trừ `nil` và `false`
   2. Đối với `string` mặc dù emplty nhưng vẫn `true`.
      1. ![tóm tắt T and F](image.png)
4. Types
   1. String
   2. Number
   3. Boolean
   4. Nil: là 1 giá trị rỗng đặc biệt mà nó trả về khi Liquid không có kết quả. Nó không phải là chuối với ký tự `'nil'`.
   5. Array
   6. EmptyDrop:
      1. là một đối tượng đặc biệt trong liquid, được trả về khi bạn cố gắng truy cập 1 đối tượng đã bị xóa hoặc không tồn tại. Điều này giúp tránh lỗi khi truy cập dữ liệu không hợp lệ,

         1. ```liquid
               {% assign page_1 = pages["about-us"] %}
               {% assign page_2 = pages["contact"] %}
               {% assign page_3 = pages["blog"] %}

               {% if page_1 == empty %}
               Page 1 is EmptyDrop.
               {% endif %}

               {% if page_2 == empty %}
               Page 2 is EmptyDrop.
               {% endif %}

               {% if page_3 == empty %}
               Page 3 is EmptyDrop.
               {% endif %}
            
            ```

5. Variations của Liquid
   1. Liquid có nhiều biến thể (variations) được sử dụng trong các hệ thống khác nhau. Dưới đây là một số biến thể phổ biến của Liquid và sự khác nhau giữa chúng.
      1. Shopify Liquid
      2. Jekyll Liquid
      3. Eleventy Liquid
6. Dấu `-` để loại bỏ khoảng trắng trong cú pháp `{{}}` và `{%%}`
   1. `{{- variable}}` hoặc `{%- tag %}` -> Xóa khoảng trắng bên trái
   2. `{{ variable -}}` hoặc `{% tag -%}` -> Xóa khoảng trắng bên phải
   3. `{{- variable -}}` hoặc `{%- tag -%}` -> Xóa khoảng trắng ở cả hai bên

## Tags

### Luồng điều khiển

1. if
2. unless: ngang với !=
3. elsif/ else

   1. ```liquid
            <!-- If customer.name = "anonymous" -->
            {% if customer.name == "kevin" %}
               Hey Kevin!
            {% elsif customer.name == "anonymous" %}
               Hey Anonymous!
            {% else %}
               Hi Stranger!
            {% endif %}

            <!--Output: Hey Anonymous  -->
      ```

4. case/when

   1. ```liquid
            {% assign handle = "cake" %}
            {% case handle %}
            {% when "cake" %}
               This is a cake
            {% when "cookie", "biscuit" %}
               This is a cookie
            {% else %}
               This is not a cake nor a cookie
            {% endcase %}

            <!--Output: This idd a cake  -->
      ```

### Lặp

1. for

   1. ```liquid
         {% for product in collection.product %}
            {{ product.title }}
         {%endfor%}      
      ```

2. for else: chạy vào else thì length = 0
3. break: thoát khỏi vòng lặp
4. continue: bỏ qua lần lặp hiện tại

5. for chứa parameters
   1. limit: giới hạn lần lặp

      ```liquid
              <!-- if array = [1,2,3,4,5,6] -->
               {% for item in array limit:2 %}
               {{ item }}
               {% endfor %}

               # output: 1 2    
      ```

   2. offset: bắt đầu từ vị trí index

      ```liquid
            #VD1:

             <!-- if array = [1,2,3,4,5,6] -->
            {% for item in array offset:2 %}
            {{ item }}
            {% endfor %}

            # output: 3 4 5 6

            #VD2:

            <!-- if array = [1,2,3,4,5,6] -->
            {% for item in array limit: 3 %}
            {{ item }}
            {% endfor %}
            {% for item in array limit: 3 offset: continue %}
            {{ item }}
            {% endfor %}
            
            #output: 
               # 1 2 3
               # 4 5 6
            
            # sử dụng offset: continue để tiếp tục vòng lặp ngay sau vị trí đã dừng của vòng lặp trước

            📌 Lưu ý khi dùng offset: continue
               1. Chỉ có tác dụng trong nhiều vòng lặp liên tiếp.
               2. Không hoạt động nếu vòng lặp trước đó không có limit.
               3. Nếu danh sách nhỏ hơn tổng số phần tử lặp, vòng lặp cuối có thể hiển thị ít hơn số phần tử mong muốn.
      ```

   3. RANGE

      ```liquid
         {% assign num = 4 %}
         {% assign range = (1..num) %}
         {% for i in range %}
         {{ i }}
         {% endfor %}

         #OUTPUT: 
            # 3 4 5
            # 1 2 3 4
      ```

   4. reversed: giúp đảo ngược thứ tự vòng lặp

      ```liquid
            <!-- if array = [1,2,3,4,5,6] -->
            {% for item in array reversed %}
            {{ item }}
            {% endfor %}

            #output: 6 5 4 3 2 1
      ```

6. forloop (object): 1 biến đặc biệt có sẵn bên trong vòng lặp for. Nó giúp bạn lấy thông tin về `vòng lặp hiện tại`, như `chỉ mục`, `số lần lặp`, `phần từ đầu/ cuối`

   1. **Thuộc tính**       |     **Ý nghĩa**
      ---------------------|-----------------------------------------------------------------
      forloop.index        |  Số thứ tự (bắt đầu từ 1) của phần tử trong vòng lặp.
      forloop.index0       |  Số thứ tự (bắt đầu từ 0) của phần tử trong vòng lặp.
      forloop.rindex       |  Số thứ tự đếm ngược (từ N về đến 1) của phần tử .
      forloop.rindex0      |  Số thứ tự đếm ngược (từ N-1 về đến 0) của phần tử.
      forloop.first        |  Trả về true nếu đây là phần tử đầu tiên trong vòng lặp.
      forloop.last         |  Trả về true nếu đây là phần tử cuối cùng trong vòng lặp.
      forloop.length       |  Tổng số phần tử trong vòng lặp.

   ```liquid
         {% for fruit in fruits %}
         {% if forloop.first %}
            <strong>Đầu tiên: {{ fruit }}</strong> <br>
         {% elsif forloop.last %}
            <strong>Cuối cùng: {{ fruit }}</strong> <br>
         {% else %}
            {{ fruit }} <br>
         {% endif %}
         {% endfor %}

         # Đầu tiên: Táo  
           Cam  
           Xoài  
           Cuối cùng: Nho  
   ```

7. Cycle: sử  dụng trong `for` để  giúp lặp lại một tập hợp giá trị theo thứ tự, mỗi lần lặp sẽ chọn một giá trị kế tiếp trong danh sách, rồi quay lại từ đầu khi hết danh sách.
   1. Khi cần xen kẽ màu nền, class CSS.
   2. Khi muốn luân phiên giữa các giá trị trong vòng lặp.
   3. Khi cần nhóm các giá trị có quy luật mà không cần viết logic if/else phức tạp.

   ```liquid
         <!-- Iteration 1 -->
         {% for i in (1..4) -%}
         {% cycle 'one', 'two', 'three' %}
         {%- endfor %}

         <!-- Iteration 2 -->
         {% for i in (1..4) -%}
         {% cycle 'one', 'two', 'three' %}
         {%- endfor %}

         => DO I1 và I2 k có key group nên nó sẽ chia sẻ chu kỳ với 

         output: 
         one 
         two
         three
         one
         => hết chu kỳ 1
         => do chia sẻ chu kỳ nên nó sẽ chạy tiếp
         two
         three
         one
         two



         <!-- Iteration 3 -->
         {% for i in (1..4) -%}
         {% cycle 'group_1': 'one', 'two', 'three' %}
         {%- endfor %}

         <!-- Iteration 4 -->
         {% for i in (1..4) -%}
         {% cycle 'group_2': 'one', 'two', 'three' %}
         {%- endfor %}

         => có key nên là 1 group khác nhau chạy riêng chu kỳ

         => chu kỳ 1
         one two three one
         => chu kỳ 2
         one two three one
   ```

8. `tablerow`: sinh HTML table rows for mọi phần tử trong array.
   1. cols: để định nghĩa số colunms của 1 row. Nếu k định nghĩa cols mặc định sẽ là 1 dòng và mỗi phần tử 1 cột
   2. tablerowloop (object): 1 biến đặc biệt có bên trong tablerow cho phép lấy thông tin như:
      1. col: là cột hiện tại (bắt đầu từ 1)
      2. col0: là cột hiện tại (bắt đầu từ 0)
      3. row: là hàng hiện tại (bắt đầu từ 1)
      4. col_frist: trả về true nếu là cột đầu tiên
      5. col_last: trả về true nếu là cột cuối cùng
9. `paginate`: chia tách phần tử trong mảng sang nhiều trang.
   1. Do for loops giới hạn 50 iterations trên page, nên cần dùng `paginate` để lặp nhiều hơn 50 phần tử
   2. 🧩 Cú pháp

      ```liquid
            {% paginate collection.products by 12 %}
            {% for product in collection.products %}
               {{ product.title }}
            {% endfor %}

            <div>
               {% if paginate.previous %}
                  <a href="{{ paginate.previous.url }}">Previous</a>
               {% endif %}

               Page {{ paginate.current_page }} of {{ paginate.pages }}

               {% if paginate.next %}
                  <a href="{{ paginate.next.url }}">Next</a>
               {% endif %}
            </div>
            {% endpaginate %}

            {%comment%}
               📖 Giải thích:
               collection.products: Đây là mảng bạn muốn phân trang.

               by 12: Số phần tử mỗi trang (tuỳ bạn muốn).

               {% paginate ... %} bắt đầu phần paginate.

               {% endpaginate %} kết thúc paginate.

               Bên trong bạn dùng vòng for để hiển thị item trên mỗi trang.
            {%endcomment%}

         ```

   3. Các thuộc tính của `pagiante`

      | Thuộc tính               | Giải thích                    |
      |--------------------------|-------------------------------|
      | `paginate.items`         | Tổng số item trong collection |
      | `paginate.pages`         | Tổng số trang                 |
      | `paginate.current_pages` | Trang hiện tại                |
      | `paginate.previous`      | Thông tin trang trước(nếu có) |
      | `pagiante.previous.url`  | URL của trang trước           |
      | `pagiante.next`          | Thông tin trang sau(nếu có)   |
      | `paginate.next.url`      | URL của trang sau             |

### Template

1. comment
2. echo: để thay cho `{{}}`. Giúp in ra dữ liệu
3. liquid: để viết nhiều lệnh liquid `trong 1 khối`, giúp code gọn hàng hơn so với việc mở nhiều `{% %}`.
4. raw: dùng để hiện thị mã liquid như `văn bản`, không bị xử lý

### Theme tags

1. content_for: giúp `ghi nội dung vào một vùng placeholder`, để chèn vào chỗ khác trong theme layout, giúp `render các vùng block hoặc group block động` trong template `sectioned theme`.
2. Hai loại type:
         | Type     | Mô tả                                                                         |
         |----------|-------------------------------------------------------------------------------|
         | `blocks` | Render `nhiều block` từ vùng template hoặc `section group`                    |
         | `block`  | Render `1 block duy nhất`, hay dùng khi bạn đang loop qua block trong section |