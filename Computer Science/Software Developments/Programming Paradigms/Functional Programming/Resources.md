#Functional_Programming 
Các tính chất của  FP
- Deterministic
- Total Function: có thể hiểu là sẽ luôn luôn trả về giá trị có sự tương ưng 1 và chỉ 1 giữ 2 miền giá trị >< Partical Function
- never have side effect (means read or write something outside body function) == Pure
- Immutability
	- No variables, only constants (Aliases)
	- Each transformation or function call create new value
- Pure function has no side effect , is deterministic and total 
- Referential Transparency
	- **nó có thể được thay thế bằng giá trị của chính nó mà không làm thay đổi hành vi của chương trình**.
	- square x = x * x -> gọi square 4 sẽ luôn trả về 16. Ở đây, square 4 luôn trả về 16. Bạn có thể thay square 4 bằng 16 ở bất kỳ chỗ nào trong chương trình, và chương trình vẫn hoạt động đúng. ➡️ Điều đó có nghĩa là square 4 referentially transparent.

# Function Composition
$$ Let~h = f \circ g $$
$$(f∘g)(x)=f(g(x))$$

