Là một lập trình viên, dù có là lập trình viên web hay lập trình viên mobile, chắc hẳn các bạn cũng không quá xa lạ với form input dữ liệu. Forms là phương tiện thông dụng nhất để thu thập thông tin người dùng trên ứng dụng của bạn. Tuy nhiên, trong trường hợp số lượng dữ liệu bạn cần thu thập lớn, dẫn đến form nhập dữ liệu của bạn sẽ phải bao gồm nhiều trường khác nhau. Nếu ứng dụng của bạn chỉ làm một form đơn, tức là một form chứa tất cả các trường bạn muốn thu thập thông tin từ người dùng thì vấn đề hiển nhiên mà chắc hẳn ai cũng nhìn ra được đó là việc form của bạn sẽ rất dài, dẫn đến việc giao diện xấu là điều trước tiên, vấn đề tiếp theo đó là trong quá trình người dùng nhập dự liệu nếu có nhập sai một trường nào đó thì phải cuộn trang lên trên hay xuống dưới để nhập lại trường đó, Rất bất tiện đúng không nào? Trong trường hợp này thì điều đầu tiên mà một người lập trình viên như chúng ta cần phải nghĩ tới đó là sử dụng multiple-step form. Cho người dùng nhập dữ liệu qua nhiều bước. Với việc xử dụng multiple-step form thì chúng ta có thể giải quyết được giao diện cho ứng dụng, cũng như giảm thiểu thao tác cho người dùng. Làm cho ứng dụng của chúng ta thông minh hơn. Trong bài viết hôm nay, tôi xin giới thiệu với các bạn cách sử dụng **NodeJs** và **Semantic UI** để xây dựng lên multiple-step form.

# Bắt đầu

Trước tiên, hãy dùng _create-react-app_ để tạo mới một React project với câu lệnh sau:

```
$ npm install -g create-react-app
$ create-react-app multistep-form
```

Sau khi chạy xong câu lệnh trên, cấu trúc thư mục project của chúng ta như sau:

```
|---public
|------favicon.ico
|------index.html
|------manifest.json
|---src
|------app.css
|------app.js
|------app.test.js
|------index.css
|------index.js #
|------logo.svg
|------registerServiceWorker.js
|---.gitignore
|---package.json
|---README.md
|---yarn.lock
```

Ở đây, chúng ta sử dụng _yarn_ để quản lý các package sẽ được xử dụng trong project. Trước hết, hãy dùng yarn để thêm vào package **senmantic**

```
yarn add semantic-ui-react
```

Để thêm css senmantic package vào trong ứng dụng của bạn, thì hãy nhớ thêm đoạn code sau vào file _index.html_

```HTML
// index.html
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.2.12/semantic.min.css"></link>
```
Công việc chuẩn bị đã hoàn tất, đi vào thực hành thôi ae.

# Tạo Components

Component đầu tiền chúng ta cần hãy đặt tên là **MainForm.js**, Component này sẽ thực hiện phần lớn chức năng của ứng dụng mà chúng ta sẽ làm. Hãy tạo file và thêm đoạn code sau.

```Javascript
// MainForm.jsx
import React, { Component } from 'react';
import UserDetails from './UserDetails';
import PersonalDetails from './PersonalDetails';
import Confirmation from './Confirmation';
import Success from './Success';

class MainForm extends Component {
    state = {
        step: 1,
        firstName: '',
        lastName: '',
        email: '',
        age: '',
        city: '',
        country: ''
    }

    nextStep = () => {
        const { step } = this.state
        this.setState({
            step : step + 1
        })
    }

    prevStep = () => {
        const { step } = this.state
        this.setState({
            step : step - 1
        })
    }

    handleChange = input => event => {
        this.setState({ [input] : event.target.value })
    }

    render(){
        const {step} = this.state;
        const { firstName, lastName, email, age, city, country } = this.state;
        const values = { firstName, lastName, email, age, city, country };
        switch(step) {
        case 1:
            return <UserDetails 
                    nextStep={this.nextStep} 
                    handleChange = {this.handleChange}
                    values={values}
                    />
        case 2:
            return <PersonalDetails 
                    nextStep={this.nextStep}
                    prevStep={this.prevStep}
                    handleChange = {this.handleChange}
                    values={values}
                    />
        case 3:
            return <Confirmation 
                    nextStep={this.nextStep}
                    prevStep={this.prevStep}
                    values={values}
                    />
        case 4:
            return <Success />
        }
    }
}

export default MainForm;

```

Hãy nhìn lại code một chút, Trong components trên, chúng ta khởi tạo giá trị mặc định của step là 1. và section đầu tiên được sinh ra. Người dùng có thể skip bước hiện tại để chuyển sang bước tiếp theo hoặc là back về bước trước thông qua các hàm **prevStep** hoặc **nextStep**. Trong các hàm này, chúng ta sẽ thay đổi giá trị của biến _step_ bằng hàm **setState**. Từ đó cho phép người dùng có thể thay đổi hiển thị trên giao diện bằng cách thay đổi components được genered. Trong copenent trên đoạn code thể hiện điều này chính là đoạn mã **switch() {} case:**. Các bạn có thể thấy với từng step thì chúng ta trả về component khác nhau.

Trong **mainForm.js**, chúng ta có import một số compoent cần thiết vào đó, lẽ tất nhiên là tiếp theo chúng ta cần tạo ra các compoent đó. 

```Javascript
// UserDetails.js
import React, { Component } from 'react';
import { Form, Button } from 'semantic-ui-react';

class UserDetails extends Component{

    saveAndContinue = (e) => {
        e.preventDefault()
        this.props.nextStep()
    }

    render(){
        const { values } = this.props;
        return(
            <Form >
                <h1 className="ui centered">Enter User Details</h1>
                <Form.Field>
                    <label>First Name</label>
                    <input
                    placeholder='First Name'
                    onChange={this.props.handleChange('firstName')}
                    defaultValue={values.firstName}
                    />
                </Form.Field>
                <Form.Field>
                    <label>Last Name</label>
                    <input
                    placeholder='Last Name'
                    onChange={this.props.handleChange('lastName')}
                    defaultValue={values.lastName}
                    />
                </Form.Field>
                <Form.Field>
                    <label>Email Address</label>
                    <input
                    type='email'
                    placeholder='Email Address'
                    onChange={this.props.handleChange('email')}
                    defaultValue={values.email}
                    />
                </Form.Field>
                <Button onClick={this.saveAndContinue}>Save And Continue </Button>
            </Form>
        )
    }
}

export default UserDetails;
```
Đây là component để sinh ra form cho step 1 trong file formMain.js bên trên. Trong component này chúng ta sẽ tạo ra một số trường input nhưu First Name, Full Name, Email Address, ... Function **saveAndContinue** sẽ di chuyển người dùng tới component tiếp theo, sau khi người dùng đã điền đầy đủ thông tin. Chú ý rằng, mỗi trường đều có giá trị default.

Tiếp theo

```Javascript
// PersonalDetails.js
import React, { Component } from 'react';
import { Form, Button } from 'semantic-ui-react';
import { throws } from 'assert';

class PersonalDetails extends Component{
    saveAndContinue = (e) => {
        e.preventDefault();
        this.props.nextStep();
    }

    back  = (e) => {
        e.preventDefault();
        this.props.prevStep();
    }

    render(){
        const { values } = this.props
        return(
        <Form color='blue' >
            <h1 className="ui centered">Enter Personal Details</h1>
            <Form.Field>
                <label>Age</label>
                <input placeholder='Age'
                onChange={this.props.handleChange('age')}
                defaultValue={values.age}
                />
            </Form.Field>
            <Form.Field>
                <label>City</label>
                <input placeholder='City'
                onChange={this.props.handleChange('city')}
                defaultValue={values.city}
                />
            </Form.Field>
            <Form.Field>
                <label>Country</label>
                <input placeholder='Country'
                onChange={this.props.handleChange('country')}
                defaultValue={values.country}
                />
            </Form.Field>
            <Button onClick={this.back}>Back</Button>
            <Button onClick={this.saveAndContinue}>Save And Continue </Button>
        </Form>
        )
    }
}

export default PersonalDetails;
```

Tương tự như UserDetail.js, trong component này chúng ta cũng tạo ra form với một số trường như City, Age, ... Trong component này chúng ta địn nghĩa thêm function back() và function saveAndContinue() để back về step trước cũng như chuyển sang step tiếp theo.

```Javascript
// Confirmation.js
import React, { Component } from 'react';
import { Button, List } from 'semantic-ui-react';

class Confirmation extends Component{
    saveAndContinue = (e) => {
        e.preventDefault();
        this.props.nextStep();
    }

    back  = (e) => {
        e.preventDefault();
        this.props.prevStep();
    }

    render(){
        const {values: { firstName, lastName, email, age, city, country }} = this.props;

        return(
            <div>
                <h1 className="ui centered">Confirm your Details</h1>
                <p>Click Confirm if the following details have been correctly entered</p>
                <List>
                    <List.Item>
                        <List.Icon name='users' />
                        <List.Content>First Name: {firstName}</List.Content>
                    </List.Item>
                    <List.Item>
                        <List.Icon name='users' />
                        <List.Content>Last Name: {lastName}</List.Content>
                    </List.Item>
                    <List.Item>
                        <List.Icon name='mail' />
                        <List.Content>
                            <a href='mailto:jack@semantic-ui.com'>{email}</a>
                        </List.Content>
                    </List.Item>
                    <List.Item>
                        <List.Icon name='calendar' />
                        <List.Content>{age} Years</List.Content>
                    </List.Item>
                    <List.Item>
                        <List.Icon name='marker' />
                        <List.Content>{city}, {country}</List.Content>
                    </List.Item>
                </List>

                <Button onClick={this.back}>Back</Button>
                <Button onClick={this.saveAndContinue}>Confirm</Button>
            </div>
        )
    }
}

export default Confirmation;
```

Sau khi hoàn thành nhập thông tin ở 2 component trước, thì chuyển tới component confirm này, component này có nhiệm vụ hiển thị lại những thông ti mà người dùng đã nhập để người dùng kiểm tra lại một lần cuối, nếu có sai sót ở thông tin nào người dùng có thê rnhanas nút Back để về sửa lại thông tin đó. Còn nếu thông tìn đều đã chính xác hoàn toàn, thì người dùng nhất nút Confirm. Hệ thống sẽ lưu data và chuyển sang màn hình Success (được tạo ra bới component Success.js)

```Javascript
// Success.jsx
import React, { Component } from 'react';

class Success extends Component{
    render(){
        return(
            <div>
                <h1 className="ui centered">Details Successfully Saved</h1>
            </div>
        )
    }
}

export default Success;
```

Tất cả các component đều đã hoàn tất. Bây giờ việc quan trọng nhất của chúng ta là update lại file app.js

```Javascript
// app.js
import React, { Component } from 'react';
import './App.css';
import MainForm from './components/MainForm';
import { Container } from 'semantic-ui-react';

class App extends Component {

  render() {
    return(
      <Container textAlign='center'>
        <MainForm />
      </Container>     )
  }
}

export default App;
```
Để ý rằng, chúng ta sẽ để form trong _Container_ Component được import từ **Senmanic** thêm thuộc tính căn giữa cho dễ quan sát. Bên trong component này chúng ta chỉ cần gọi đến mainForm component đã được định nghĩa ở trên. Như vậy là chúng ta đã hoàn thành việc tạo multiple-step form. Đây chỉ là ví dụ cơ bản, nếu các bạn quan tâm, hãy đào sâu nghiên cứu và cải tiến thêm nhé. Chúc các bạn thành công!^^ 
