### [과제] 숙련주차 과제 답

---

## 1. 아이템 추가가 안됨

#### 원인 : form에서 dispatch안해주고 있음.

`Form.js`

### Before

        const onSubmitHandler = (event) => {
            event.preventDefault();
            if (todo.title.trim() === "" || todo.body.trim() === "") return;
            setTodo({
            id: 0,
            title: "",
            body: "",
            isDone: false,
            });
        };

### After

        const dispatch = useDispatch(); // 추가

        const onSubmitHandler = (event) => {
            event.preventDefault();
            if (todo.title.trim() === "" || todo.body.trim() === "") return;
            dispatch(addTodo(todo));  / /추가
            setTodo({
            id: id, //4번에서 수정한 부분
            title: "",
            body: "",
            isDone: false,
            });
        };

---

## 2. 추가하기 버튼 누르면 기존 아이템 사라짐.

#### 원인: todos에서 받았지만, 기존꺼+새로운거로 통째로 새로 넣어줘야 하는데 새로운것만 넣어버려서 기존데이터가 사라짐.

      console.log('added' + JSON.stringify(action.payload))
      console.log('state' + JSON.stringify(state.todos))

#### 찍어보고, 기존data와 새로운 data의 위치를 확인, 아래 코드 추가함.

`todos.js`

### Before

        switch (action.type) {
            case ADD_TODO:
            return {
                ...state,
                todos: [action.payload],
            };

### After

        switch (action.type) {
            case ADD_TODO:
            return {
                ...state,
                todos: state.todos.concat(action.payload)
            };

---

## 3. 삭제 기능 동작 안함

#### 원인 : reducer에 삭제 기능이 빠져있음

`todos.js`

### Before

        없었음

### After (reducer에 추가)

        case DELETE_TODO:
        return { ...state, todos: state.todos.filter((todo) => todo.id !== action.payload) }

---

## 4. 상세페이지 진입시 데이터

`Form.jsx`

#### 원인1: 일단 id 값부터 제대로 유니크하게 할당하도록 수정 (기존에 계속 중복id값 할당됨)

### Before

            const [todo, setTodo] = useState({
            id: 0,
            title: "",
            body: "",
            isDone: false,
            });
            const onSubmitHandler = (event) => {
            event.preventDefault();
            if (todo.title.trim() === "" || todo.body.trim() === "") return;
            setTodo({
            id: 0,
            title: "",
            body: "",
            isDone: false,
            });
            };

### After

            const [todo, setTodo] = useState({
            id: id, //0이었음
            title: "",
            body: "",
            isDone: false,
            });
            const onSubmitHandler = (event) => {
            event.preventDefault();
            if (todo.title.trim() === "" || todo.body.trim() === "") return;
            dispatch(addTodo(todo));
            setTodo({
            id: id, //0이었음
            title: "",
            body: "",
            isDone: false,
            });
            };

#### 원인2:상세페이지에서 값을 잘못 불러오고 있음. (직접적인 원인)

`Detail.jsx`

### Before

        const Detail = () => {
        const dispatch = useDispatch();
        const todo = useSelector((state) => state.todos.todo);

        const { id } = useParams();
        const navigate = useNavigate();

### After

        const Detail = () => {
        const dispatch = useDispatch();
        const { id } = useParams();
        const todos = useSelector((state) => state.todos.todos); // todo아니고 todos이어야 함.
        const todo = todos.find((a) => a.id === id); // 위에꺼 s붙여서 배열 가져오고, 아래꺼는 신설.
        const navigate = useNavigate();

---

## 5. 완료된 카드 진입시 정보 안나옴.

#### 원인: 아이디값 파라미터로 전달해야하는데 index를 줘버림.

` List.jsx`

### Before

        <StLink to={`/${index}`} key={todo.id}>

### After

        <StLink to={`/${todo.id}`} key={todo.id}>

---

## 6. 취소버튼에 id전달 안되어있음,

`List.jsx`

### Before

      <StButton
                    borderColor="green"
                    onClick={onToggleStatusTodo}
                  >
                    {todo.isDone ? "취소!" : "완료!"}
                  </StButton>

### After

          <StButton
                    borderColor="green"
                    onClick={() => {
                      onToggleStatusTodo(todo.id);
                    }}
                  >
                    {todo.isDone ? "취소!" : "완료!"}
                  </StButton>
