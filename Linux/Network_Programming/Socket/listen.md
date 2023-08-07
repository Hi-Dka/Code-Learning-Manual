# listen()

在套接字编程中，`listen()` 函数用于将一个已绑定的套接字设置为被动监听状态，以便接受连接请求。下面是关于 `listen()` 函数的详细说明：

函数原型：
```c
int listen(int sockfd, int backlog);
```

参数解释：
- `sockfd`：套接字描述符，即要进行监听的套接字。
- `backlog`：指定监听队列的最大长度，即同时等待被接受的连接请求的数量。

函数返回值：
- 如果监听成功，返回值为0。
- 如果发生错误，返回值为-1，并设置相应的错误代码，可以使用 `perror()` 函数输出错误信息。

示例用法：
```c
#include <stdio.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main() {
    int sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return 1;
    }

    struct sockaddr_in server_address;
    server_address.sin_family = AF_INET;
    server_address.sin_addr.s_addr = INADDR_ANY;
    server_address.sin_port = htons(8080);

    int bind_result = bind(sockfd, (struct sockaddr*)&server_address, sizeof(server_address));
    if (bind_result == -1) {
        perror("bind");
        return 1;
    }

    int listen_result = listen(sockfd, 5);
    if (listen_result == -1) {
        perror("listen");
        return 1;
    }

    printf("Listening for incoming connections...\n");

    return 0;
}
```

解释：
- 在这个示例中，我们先创建了一个套接字并将其绑定到指定的地址和端口。
- 然后，调用 `listen()` 函数将套接字设置为监听状态。
- `backlog` 参数指定了监听队列的最大长度，即可以同时等待接受的连接请求的数量。
- 如果监听成功，将输出监听成功的消息。
- 如果发生错误，将输出错误信息。

注意事项：
- 在调用 `listen()` 函数之前，必须先创建套接字并确保套接字已经绑定到一个地址。
- `backlog` 参数决定了同时等待接受的连接请求的数量，具体的支持数目由系统决定，但实际上可以接受的连接数量可能受到系统资源的限制。
- 监听的套接字通常是服务器程序中的套接字，用于等待客户端的连接请求。

