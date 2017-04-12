
~~~
-------------------------------------------------------------------------------
---------------------------Containers and Components---------------------------
-------------------------------------------------------------------------------
~~~

---

# Lets start with `connect()`

## `connect()` takes 2 args and returns a function that takes a single arg

## which means this...

    export default connect(mapState, mapActions)(MyComponent);

## is the same as this...

    const preloadedConnect = connect(mapState, mapActions);
    export default preloadedConnect(MyComponent);

---

# Separate into files...

## components/MyComponent.js

    class MyComponent extends Component {
      static propTypes = {
        firstName: PropTypes.string,
        lastName: PropTypes.string,
        onSave: PropTypes.string,
      };
      render() {
        ...
      }
    }
    export default MyComponent;

## containers/MyContainer.js

    const mapState = ...;
    const mapActions = ...;
    export default connect(mapState, mapActions);

---

# Ok... but how do i use it...???

## pages/MyPage.js

    import MyContainer from '../containers/MyContainer';
    import MyComponent from '../component/MyComponent';
    const ConnectedComponent = MyContainer(MyComponent);
    ...
    ...
    render() {
      return (
        <ConnectedComponent />
      );
    }

---

# So... why...???

> "Dope software brah"
> - Gentry

    describe('<MyComponent />', () => {
      it('should be unit tested', () => {
        const wrapper = mount(
          <MyComponent
            firstName="Alex"
            lastName="Either"
            onSave={() => console.log('dead to me')}
          />
        );
        expect(...).to.work.correctly;
      });
    });

---

~~~

     _   _ _   _ _____ _____
    | | | | \ | |_   _|_   _|
    | | | |  \| | | |   | |
    | | | | . ` | | |   | |
    | |_| | |\  |_| |_  | |
     \___/\_| \_/\___/  \_/
     _____ _____ _____ _____ _____
    |_   _|  ___/  ___|_   _/  ___|
      | | | |__ \ `--.  | | \ `--.
      | | |  __| `--. \ | |  `--. \
      | | | |___/\__/ / | | /\__/ /
      \_/ \____/\____/  \_/ \____/
    ______ _____ _    _
    |  ___|_   _| |  | |
    | |_    | | | |  | |
    |  _|   | | | |/\| |
    | |     | | \  /\  /
    \_|     \_/  \/  \/

~~~

---

~~~
-------------------------------------------------------------------------------
------------------------------React Lifecycle----------------------------------
-------------------------------------------------------------------------------
~~~

---

# Phases
1. Initial Creation
2. Updates
3. Unmounting

---

# Initial Creation
1. constructor()
    - setup state and initial props
2. componentWillMount()
3. render()
4. *ref callbacks*
5. componentDidMount()
    - make api calls here
    - setup any listeners / render loops

---

# Updates(state)
1. setState
2. shouldComponentUpdate(nextProps, nextState)
    - default returns true
    - ez perf gainz ftw
3. componentWillUpdate(nextProps, nextState)
4. render()
5. *ref callbacks*
6. componentDidUpdate(prevProps, prevState)

---

# Updates(props)
1. props from parent component
2. componentWillRecieveProps(nextProps)
3. shouldComponentUpdate(nextProps, nextState)
    - default returns true
    - ez perf gainz ftw
4. componentWillUpdate(nextProps, nextState)
5. render()
6. *ref callbacks*
7. componentDidUpdate(prevProps, prevState)

---

# Unmounting
1. component gets removed from render tree
2. componentWillUnmount()
    - disconnect all event listeners / render loops

---

# Render perf antipatterns
creating new functions in render (returning an arrow function)

    onClick() {
        return () => this.setState();
    }
    render() {
        return (
            <MyComponent
                onClick={this.onClick()}
            />
        );
    }

instead...

    onClick = () => {
        this.setState();
    }
    render() {
        return (
            <MyComponent
                onClick={this.onClick}
            />
        );
    }

---

# Render perf antipatterns
creating new objects in render (the terrible `{{`)

    render() {
        return (
            <MyComponent
                things={{
                    text: 'Do Something',
                    onClick: this.doSomething,
                }}
            />
        );
    }

instead...

    things = {
        text: 'Do Something',
        onClick: this.doSomething,
    }
    render() {
        return (
            <MyComponent
                things={this.things}
            />
        );
    }

---

# Render perf antipatterns
creating new objects in render (the terrible `|| {}`)

    render() {
        return (
            <MyComponent
                things={this.props.thing || {}}
            />
        );
    }

instead...

    defaultThing = {}
    render() {
        return (
            <MyComponent
                things={this.props.thing || this.defaultThing}
            />
        );
    }

---

# Reference Material

https://medium.com/@esamatti/react-js-pure-render-performance-anti-pattern-fb88c101332f
