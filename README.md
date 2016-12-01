
Reference:
http://wiki.friendlyarm.com/wiki/index.php/NanoPi_M1



# More Aptina changes

### Issues
- There was no way to modify the individual resistor value
- There was mismatch in structure in 
- The `i2c` client data was not found properly by 
- Image size was incorrect `768` in place of `752`

### Problems

Below was not working as expected
```c
struct i2c_client *client = v4l2_get_subdevdata(&mt9v032->subdev);
```
So used global `i2c` client

The structure which is passed in `v4l2_s_ctrl` was incorrect
```c
v4l2_subdev_call(dev->sd,core,s_ctrl,ctrl);
```

### Changes
- Made `I2C` client data global and populated this for `mt9v` as this will always be fixed. It is observed that it is static even on startup but anyhow after `mt9v` is initialized once `i2c` client is fixed
- Used `V4L2_CID_GAIN` id in s_ctrl to write value on any resistor
- To pass resistor address added one extra parameter in `v4l2_ctrl` structure

There is a mismatch in v4l2_control in videodev2.h lichee kernel and brandy tool chain

In lichee kernel:
```c
/*
 *	C O N T R O L S
 */
struct v4l2_control {
	__u32		     id;
	__s32		     value;
	__u32				 user_pt;	/* Add user pointer by raymonxiu */	
};
```

in tool chain

```c
/*
 *	C O N T R O L S
 */
struct v4l2_control {
	__u32		     id;
	__s32		     value;
};

```

Modify toolchain structure and add user_pt as it is used to pass the register address.

