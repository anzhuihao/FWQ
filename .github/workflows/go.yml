package main

import (
	"fmt"
	"io/ioutil" // 用于读取文件
	"net/http"  // 用于发起网络请求
	"time"      // 用于处理时间相关的操作

	"gopkg.in/yaml.v2" // 引入yaml包以解析YAML文件
)

// Config 结构体用于映射YAML配置文件的结构
type Config struct {
	Sites []struct {
		URL      string `yaml:"url"`      // 网站的URL
		Interval string `yaml:"interval"` // 访问间隔，字符串格式
	} `yaml:"sites"`
}

// readConfig 函数用于从指定路径读取并解析YAML配置文件
func readConfig(path string) (*Config, error) {
	bytes, err := ioutil.ReadFile(path) // 读取文件内容
	if err != nil {
		return nil, err // 读取失败，返回错误
	}

	var config Config
	err = yaml.Unmarshal(bytes, &config) // 解析YAML内容到config结构体
	if err != nil {
		return nil, err // 解析失败，返回错误
	}

	return &config, nil // 返回解析后的配置
}

// visitURL 函数用于定时访问指定的URL
func visitURL(url string, interval time.Duration) {
	for { // 无限循环，持续执行
		resp, err := http.Get(url) // 发起GET请求
		if err != nil {
			fmt.Printf("Error visiting %s: %s\n", url, err) // 打印访问错误
		} else {
			resp.Body.Close() // 关闭响应体，释放资源
			fmt.Printf("Visited %s, Status Code: %d\n", url, resp.StatusCode) // 打印访问成功信息
		}
		time.Sleep(interval) // 按照指定间隔等待
	}
}

// main 函数是程序的入口点
func main() {
	config, err := readConfig("config.yml") // 读取配置文件
	if err != nil {
		panic(err) // 读取失败，终止程序
	}

	for _, site := range config.Sites { // 遍历配置中的所有网站
		interval, err := time.ParseDuration(site.Interval) // 解析访问间隔为time.Duration类型
		if err != nil {
			fmt.Printf("Invalid interval for %s: %s\n", site.URL, err) // 打印间隔解析错误
			continue // 跳过当前循环，继续下一个
		}

		go visitURL(site.URL, interval) // 为每个网站启动一个goroutine进行定时访问
	}

	// 防止主goroutine立即退出，导致程序结束
	select {}
}
