Created: August 30, 2019 9:28 AM
Last Edited Time: September 17, 2019 11:58 AM
Type: Error, Flutter, Mobile

1. 

`resizeToAvoidBottomPadding`is depracated in the beta channel.Same and flawless results with just

`resizeToAvoidBottomInset: false`

2.

Wrap your Padding in a SingleChildScrollView.

`@override Widget build(BuildContext context) { return Scaffold( appBar: AppBarDefault('Profile'), body: SingleChildScrollView( child: Container( padding: EdgeInsets.all(20.0), child: Column( crossAxisAlignment: CrossAxisAlignment.start, children: <Widget>...`

To this method work you have to remove the `resizeToAvoidBottomInset: false`.

3.

```
@override Widget build(BuildContext context) { 
  return Scaffold(
    body: Center(
          child: SingleChildScrollView(
            child: Column( 
              mainAxisAlignment: MainAxisAlignment.center,
              crossAxisAlignment: CrossAxisAlignment.center,       
              children: <Widget>[...
```

I solved my problem doing like this when I want my form to be centered.