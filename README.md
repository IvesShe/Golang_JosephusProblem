# Golang JosephusProblem 約瑟夫問題

試著用Golang處理一些很久以前接觸過的數據結構，順便複習及記筆記

## Josephus問題

1. 設編號為1、2、...、n的n個人圍坐一圈。
2. 約定編號為k(1 <= k <= n)的人從1開始報數，數到m的那個人出列。
3. 他的下一位又從1開始報數，數到m的那個人又出列，依此類推，直到所有人出列為止。
4. 由此產生一個出隊編號的序列。

# 思路
1. 用一個不帶頭結點的循環鏈表(circular linked list)來處理。
2. 先構成一個有n個結點的單循環錄表，然後由k結點從1開始計數，計到m時，對應結點從鏈表中刪除。
3. 然後再從被刪除結點的下一個結點又從1開始計算，直到最後一個結點從鏈表中刪除，該算法結束。

# 執行畫面

![image](./images/20201212122356.png)

![image](./images/20201212122430.png)

# 代碼

main.go
```go
package main

import (
	"fmt"
)

type Node struct {
	No   int   // 編號
	Next *Node // 指向下一個節點的指針
}

// 構成單向的循環列表
// num: 表示生成的個數
// 回傳的*Node: 返回該環形鏈表第一個節點的指針
func AddNode(num int) *Node {

	first := &Node{}   // 空節點
	curNode := &Node{} // 空節點(輔助用)

	// 判斷
	if num < 1 {
		fmt.Println("num的值小於1了!!!")
		return first
	}

	// 循環的構建這個環形鏈表
	for i := 1; i <= num; i++ {
		node := &Node{
			No: i,
		}

		if i == 1 {
			// 第一個節點 比較特殊
			first = node
			curNode = node
			curNode.Next = first // 單個節點形成循環
		} else {
			// 把當前節點的Next指向下一個節點
			curNode.Next = node
			// 將當前節點置換指向下一個節點
			curNode = node
			// 新的節點再將其Next指回到第一個節點
			// 構成環形鏈表
			curNode.Next = first
		}
	}

	return first
}

// 顯示單向的環形鏈表
func ShowNode(first *Node) {

	// 處理空鏈表
	if first.Next == nil {
		fmt.Println("鏈表為空!!!")
		return
	}

	// 創建一個指針，幫助遍歷
	curNode := first

	for {
		fmt.Printf("節點編號=%d -> ", curNode.No)

		// 已遍歷完成即退出
		if curNode.Next == first {
			break
		}

		// curNode移動到下一個節點
		curNode = curNode.Next
	}
}

// 依照約瑟夫問題的思路，開始玩這個遊戲
func PlayGame(first *Node, startNo int, countNum int) {

	// 處理空鏈表
	if first.Next == nil {
		fmt.Println("空的鏈表!!!")
		return
	}

	// 定義輔助指針，幫忙刪除節點
	tail := first

	// 使tail先指向環形鏈表的最後一個節點
	for {
		if tail.Next == first {
			// tail到了最後的節點
			break
		}
		tail = tail.Next
	}

	// 讓first移動到startNo，tail需緊跟著
	// 可以想像成first後面跟著tail，因為tail.Next目前是指向first
	for i := 1; i <= startNo-1; i++ {
		first = first.Next
		tail = tail.Next
	}

	fmt.Println() // 換行

	// 開始數countNum，然後就刪除first指向的小孩
	for {
		// 開始數countNum-1次，因為本身也算1次
		for i := 1; i <= countNum-1; i++ {
			first = first.Next
			tail = tail.Next
		}
		fmt.Printf("節點編號為%d 出圈\n", first.No)

		// 刪除first目前的節點
		first = first.Next // 將first置換成下一個節點
		tail.Next = first  // 讓tail的Next指向新的first節點

		// 如果tail跟first相等時，代表圈子裡只剩下一個節點
		if tail == first {
			break
		}
	}

	fmt.Printf("節點編號為%d 出圈\n", first.No)
}

func main() {

	// 產成節點數量
	nodeCount := 20
	// 從第2個節點開始數
	startNo := 2
	// 數3位(包含本身那位也要數)
	countNum := 3

	fmt.Printf("nodeCount: %v , startNo: %v , countNum: %v\n",
		nodeCount, startNo, countNum)

	first := AddNode(nodeCount)

	// 顯示
	ShowNode(first)

	// 開始遊戲
	PlayGame(first, startNo, countNum)
}
```