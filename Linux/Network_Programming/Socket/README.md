







# send()

在套接字编程中，`send()` 函数用于发送数据到已连接的套接字或发送数据到指定的套接字地址。下面是关于 `send()` 函数的详细说明：

函数原型：
```c
ssize_t send(int sockfd, const void *buf, size_t len, int flags);
```

参数解释：
- `sockfd`：套接字描述符，即用于发送数据的套接字。
- `buf`：指向要发送数据的缓冲区的指针。
- `len`：要发送数据的长度，以字节为单位。
- `flags`：可选的标志参数，用于控制发送的行为，通常可以设置为 0。

函数返回值：
- 如果发送成功，返回值为已发送数据的字节数（非负整数）。
- 如果发生错误，返回值为-1，并设置相应的错误代码，可以使用 `perror()` 函数输出错误信息。

示例用法：
```c
#include <stdio.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <string.h>

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

    struct sockaddr_in client_address;
    socklen_t client_addrlen = sizeof(client_address);

    int client_sockfd = accept(sockfd, (struct sockaddr*)&client_address, &client_addrlen);
    if (client_sockfd == -1) {
        perror("accept");
        return 1;
    }

    printf("Connection accepted from %s:%d\n", inet_ntoa(client_address.sin_addr), ntohs(client_address.sin_port));

    const char *message = "Hello, client!";
    int send_result = send(client_sockfd, message, strlen(message), 0);
    if (send_result == -1) {
        perror("send");
        return 1;
    }

    printf("Message sent: %s\n", message);

    return 0;
}
```

解释：
- 在这个示例中，我们先创建了一个套接字并将其绑定到指定的地址和端口。
- 然后，调用 `listen()` 函数将套接字设置为监听状态，并等待客户端的连接请求。
- 使用 `accept()` 函数接受客户端的连接，并获得与客户端通信的套接字描述符。
- 定义要发送的消息字符串，并使用 `send()` 函数将消息发送给客户端。
- `buf` 参数是指向要发送数据的缓冲区的指针，`len` 参数指定要发送数据的长度。
- 如果发送成功，将输出已发送的消息。

注意事项：
- 在调用 `send()` 函数之前，必须先建立连接，可以使用 `connect()` 函数或者接受的客户端套接字进行通信。
- `send()` 函数可能不会立即发送所有数据，尤其在非阻塞模式下。它可能只发送部分数据，并返回已发送的字节数。如果需要确保所有数据都发送成功，可以在循环中多次调用 `send()`，直到发送完所有数据。
- 在使用 TCP 套接字时，`send()` 函数将保证发送的数据顺序与接收方接收到的数据顺序一致。
- 对于非阻塞的套接字，可以使用 `send()` 函数的返回值来判断发送的数据量，以便进行相应的处理。


# recv()

在套接字编程中，`recv()` 函数用于接收来自已连接套接字或指定套接字地址的数据。下面是关于 `recv()` 函数的详细说明：

函数原型：
```c
ssize_t recv(int sockfd, void *buf, size_t len, int flags);
```

参数解释：
- `sockfd`：套接字描述符，即用于接收数据的套接字。
- `buf`：指向接收数据的缓冲区的指针。
- `len`：缓冲区的长度，即接收数据的最大字节数。
- `flags`：可选的标志参数，用于控制接收的行为，通常可以设置为 0。

函数返回值：
- 如果接收成功，返回值为接收到的字节数（非负整数）。
- 如果连接关闭，返回值为 0。
- 如果发生错误，返回值为-1，并设置相应的错误代码，可以使用 `perror()` 函数输出错误信息。

示例用法：
```c
#include <stdio.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <string.h>

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

    struct sockaddr_in client_address;
    socklen_t client_addrlen = sizeof(client_address);

    int client_sockfd = accept(sockfd, (struct sockaddr*)&client_address, &client_addrlen);
    if (client_sockfd == -1) {
        perror("accept");
        return 1;
    }

    printf("Connection accepted from %s:%d\n", inet_ntoa(client_address.sin_addr), ntohs(client_address.sin_port));

    char buffer[1024];
    memset(buffer, 0, sizeof(buffer));

    int recv_result = recv(client_sockfd, buffer, sizeof(buffer), 0);
    if (recv_result == -1) {
        perror("recv");
        return 1;
    }

    printf("Received message: %s\n", buffer);

    return 0;
}
```

解释：
- 在这个示例中，我们先创建了一个套接字并将其绑定到指定的地址和端口。
- 然后，调用 `listen()` 函数将套接字设置为监听状态，并等待客户端的连接请求。
- 使用 `accept()` 函数接受客户端的连接，并获得与客户端通信的套接字描述符。
- 定义一个接收数据的缓冲区，并使用 `recv()` 函数从客户端接收数据。
- `buf` 参数是指向接收数据的缓冲区的指针，`len`参数指定缓冲区的长度。
- 如果接收成功，将输出接收到的消息。

注意事项：
- 在调用 `recv()` 函数之前，必须先建立连接，可以使用 `connect()` 函数或者接受的客户端套接字进行通信。
- `recv()` 函数可能不会立即接收到所有数据，尤其在非阻塞模式下。它可能只接收部分数据，并返回已接收的字节数。如果需要确保接收完整的数据，可以在循环中多次调用 `recv()`，直到接收到预期的数据量。
- 对于 TCP 套接字，`recv()` 函数将保证接收到的数据顺序与发送方发送的数据顺序一致。
- 对于非阻塞的套接字，可以使用 `recv()` 函数的返回值来判断接收的数据量，以便进行相应的处理。

