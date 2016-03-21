# route-table
golang获取设置window路由表
```go
package main

import (
	"fmt"
	. "github.com/yijunjun/route-table"
)

func main() {
	table, err := NewRouteTable()
	if err != nil {
		panic(err.Error())
	}
	defer table.Close()
	
	rows, err := table.Routes()
	if err != nil {
		panic(err.Error())
	}

	for _, row := range rows {
		fmt.Sprintf(
			"%v--->%v---->%v",
			Inet_ntoa(row.ForwardDest, false),
			Inet_ntoa(row.ForwardMask, false),
			Inet_ntoa(row.ForwardNextHop, false),
		)
	}

	local_ip, err := GetLocalIp()
	if err != nil {
		panic(err.Error())
	}

	local_ip_uint := Inet_aton(local_ip, false)

	for _, row := range rows {
		if row.ForwardNextHop == local_ip_uint {
			// clone存在的路由,防止自定义出错
			// route add 14.215.177.37 mask 255.255.255.255 本地ip
			row.ForwardDest = Inet_aton("14.215.177.37", false)
			row.ForwardMask = Inet_aton("255.255.255.255", false)

			// 需要管理员权限,才能添加成功
			if err := table.AddRoute(row); err != nil {
				t.Error(err.Error())
			}
			break
		}
	}
}

```
