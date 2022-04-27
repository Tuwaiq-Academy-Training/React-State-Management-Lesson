# React-State-Management-Lesson

Redux

ما هو Redux

عادة ما تسير البيانات في React من أعلى إلى أسفل باستعمال props )props هي خصائص التاق التي تكون في تاق البداية)، وقد تكون هذه العملية مجهدة ومكلفة من ناحية الوقت في حال كان المشروع كبير ومعقد. فللوصول إلى state مثلاً في ملف سفلي إلى ملف علوي نحتاج أولًا إلى تمرير الـstate إلى الملف الأب parent ومن ثم تمريرها إلى الملف المراد. لتسهيل هذه العملية نقوم بفصل الstates إلى ملف خارجي(central-store) عام متاح للوصول من قبل أي مكوّن أو عنصر.
 

![](https://paper-attachments.dropbox.com/s_53CB02B642143BCF35D5C8841C7D9C5A253FB8A176C0F8A0C17C2DC0F8E8F99E_1648131554245_ReactDraw-01.jpg)



كيفية استعمال Redux من الصفر
- نقوم أولًا بتحميل مكتبة Redux و React-Redux.


    npm install react react-redux


- ثم نقوم بإنشاء مجلد منفصل يحتوي على الملفات الخاصة بالReducers في ملف `src/``reducers` src.
    هنا حيث سنقوم بتخزين كل الreducers وكل reducer سيقوم بحفظ الaction والحالة الأولية initial state الخاصة بالreducer.
    
- وظيفة الaction هي تفعيل حدث معين عن طريق مناداة كلمة معينة مرتبطة به. تقوم action بإرجاع object يحتوي على النوع(type)إلزاميه وحمولة (payload) اختيارية.


- نقوم الآن بإنشاء مجلد منفصل بإسم الحالة المراد تخزينها يحتوي على الملف الخاص بالحالة، والذي يحتوي على الحدث (action) والدالة الحقيقية المراد تنفيذها بعد استدعاء كلمة الaction. لذلك تستقبل دالة الـ Reducer الـ state المبدأية و بالإضافة إلى ال action، ثم تقوم بإجراء العملية المطلوبة بناء على ال action الذي تم إستدعائه.

`src/reducers/user/user.js`


    // 1. Declare a variable under the name "initialState", that holds an object with a
    //key for the state name and an initial value, in this case " {user: ''} ". This will //be our initial container for our state, where all the information is going to 
    //be held up.
    
    const initialState = {
        user: ''
    }
    
    // 2. Declare a function under the same name as the file. and in the values that you
    //are going to pass to it, write the following "state = initialState, {type, payload}"
    
    const user = (state = initialState, {type, payload}) => {
    
    // 3. Inside of the function we will have a switch statement where we will pass the 
    //value of the "type"
        
    switch (type) {
            case 'ADD_USER':
                return {
                    user: payload
                }
            default:
                return state
        }
    }
    
    // 4. Create an action
    
    export const addUser = (user) => {
        return {
            type: "ADD_USER",
            payload: user,
        }
    }
    export default user


- بعد ذلك نقوم بانشاء ملف store.js في مجلد ال `src/reducers/store.js` reducer ونقوم بإنشاء مخزن عن طريق دالة createStore. يمثل store حاوية الـ Global states، والتي تسمح لبقية العناصر والمكونات بالوصول إليها مباشرة. تستقبل دالة createStore جميع الreducers. إذا كان هنالك أكثر من reducer يجب إستخدام دالة combineReducers قبل إنشاء المخزن وهي دالة تقوم بدمج الreducer التي تستقبلها دالة الدمج.


    import { createStore, combineReducers } from "redux";
    // 2. import the reducer from its file location.
    import user from './user/user';
     
    // In case you have two reducer you have to use combineReducers
    //import example-1 from "./example-1/example-1";
    //import example-2 from "./example-2/example-2";
    
    // 3. Define a variable called "reducers", we will use to store all the reducer functions with the combineReducers method.
    
    //const reducers = combineReducers({ example-1, example-2 });
    
    // 4. Declare a variable called "store" and set it equal the method "createStore" and pass the reducers variable as a value as you invoque it.
    //const store = createStore(reducers);
    
    const store = createStore(user);
    export default store;

 

- ثم نقوم باستعمال Provider لتزويد مكونات React بال store التي تم إنشائها سابقًا. يمكننا إتاحته لمكونات React الخاصة بنا عن طريق وضع Provider> React-Redux> حول تطبيقنا في `src/index.js` . قم بإستدعاء مخزن Redux الذي أنشأناه للتو ، وضع <Provider> حول <App> الخاص بك ، وقم بتمرير المخزن عن طريق خاصية store.


    import React from "react";
    import ReactDOM from "react-dom";
    import "./index.css";
    import App from "./App";
    import { Provider } from "react-redux";
    // 2. Import store from its folder location.
    import store from "./reducers/store";
    ReactDOM.render(
      <React.StrictMode>
        {/* 3. Use the Provider component ti wrap the App component and add the atrributte "store" the the oppenning tag and set it equal to the store value. */}
        <Provider store={store}>
          <App />
        </Provider>
      </React.StrictMode>,
      document.getElementById("root")
    );
    


- الآن أصبح المخزن قابل للوصول من أي مكون، عن طريق إستخدام useSelector ثم نقوم بإختيار state الذي نريد الوصول إليه. ثم نقوم بإستدعاء الaction الذي بدوره سيقوم بتفعيل الreducer بواسطة دالة useDispatch.
    الآن نذهب إلى المكون الذي نريده أن يصل إلى المخزن ثم نقوم بإستدعاء  useSelector و useDispatch من مكتبة react-redux. ثم نقوم بإستدعاء الإجراء الذي قمنا بإنشائه. ثم نقوم بإنشاء useState لتحفظ فيها الحالة داخل المكون. ثم قم بإنشاء function من أجل أن تقوم بإستدعاء الaction وتمرير الحالة إلى الreducer.  
    
    import "./App.css";
    // 1. Import useDispatch, useSelector from react-redux.
    import { useDispatch, useSelector } from "react-redux";
    // 2. Import the action we want to use from the reducer.
    import { addUser } from "./reducers/user/user";
    import { useEffect, useState } from "react";
    function App() {
      // 3. Create a useState hook where you will manage the value of the user inside of the component.
      const [user, setUser] = useState();
      // useDispatch: allows me to change the value of the state in the reducer.
      const dispatch = useDispatch();
      // useSelector: allows me to acces and read the value of the state inside of the reducer.
      const state = useSelector((state) => {
        // specify which state to subscribe to (state tree => reducer => state name )
        return {
          user: state.user.user,
        };
      });
      // 4. Create a function to use the dispach method to pass the new value to the 
    //action that will get all the way
      const addNewUser = () => {
        dispatch(addUser(user));
      };
      return (
        <div className="App App-header">
          <div className="user">
            {/* 6. You can also acces the state of the reducer using the "state.user we get form the useSelector method de declared previously. */}
            <h1>{state.user}</h1>
            {/* 5. In this case we use the value inside of the setUser hook to have the addNewUser function dispatch this information to the user reducer.*/}
            <input
              onChange={(e) => {
                setUser(e.target.value);
              }}
            ></input>
            <button onClick={addNewUser}>Change user name</button>
          </div>
        </div>
      );
    }
    export default App;
    

