# 1 - Introdução

## Instalação e Configuração Inicial

1. [Download da versão para instalação](https://golang.org/dl/)

2. Descompactar o arquivo

```
$ sudo tar -C /usr/local -xzf go1.14.4.linux-amd64.tar.gz
```

3. Adicionar o caminho `/usr/local/go/bin` à variável de ambiente PATH. 

Isso pode ser feito através da adição da linha a seguir ao arquivo `/etc/profile` (para instalação global no sistema) ou `$HOME/.profile` (apenas para a conta do usuário corrente):

```
$ sudo vi /etc/profile
```

```
export PATH=$PATH:/usr/local/go/bin
```

4. Teste inicial (verificar versão)

```
$ go version
go version go1.14.4 linux/amd64
```

### Upgrade de versão

Encontrei essa dica para quando for necessário fazer o upgrade da versão do Go.

```
There's no need to delete the old copy. You can just overwrite it. Here's a one-liner that should work for Linux and MacOS:

curl --silent https://storage.googleapis.com/golang/$(curl --silent https://golang.org/doc/devel/release.html | grep -Eo 'go[0-9]+(\.[0-9]+)+' | sort -V | uniq | tail -1).$(uname -s | tr '[:upper:]' '[:lower:]')-$(case "$(uname -m)" in i*) echo '386' ;; x*) echo 'amd64' ;; *) echo 'armv61'; esac).tar.gz  | sudo tar -vxz --strip-components 1 -C $(dirname $(dirname $(which go)))

It always grabs the latest version and overwrites whatever is in your path (based on which go). I've only tested it on Linux since that's all I have access to. Let me know if you have any issues with it and I'll fix it.

PS. Here's a multi-line more verbose version:

release=$(curl --silent https://golang.org/doc/devel/release.html | grep -Eo 'go[0-9]+(\.[0-9]+)+' | sort -V | uniq | tail -1)
os=$(uname -s | tr '[:upper:]' '[:lower:]')
arch=$(case "$(uname -m)" in i*) echo '386' ;; x*) echo 'amd64' ;; *) echo 'armv61'; esac)

curl --silent https://storage.googleapis.com/golang/$release.$os-$arch.tar.gz | sudo tar -vxz --strip-components 1 -C $(dirname $(dirname $(which go)))

EDIT: When you paste the one-liner into zsh, it inserts a single backslash (\) after the first dollar sign ($). It looks like /golang/$\(curl instead of /golang/$(curl. You have to remove that backslash or you'll get the following error: zsh: parse error near ')'.
```

## Primeiros passos

### Criação do Workspace 

Recomenda-se criar a estrutura de diretórios a seguir

```
$ mkdir ~\go
$ mkdir ~\go\bin
$ mkdir ~\go\pkg
$ mkdir ~\go\src
```

* bin - código compilado
* pkg - pacotes compartilhados entre aplicações
* src - código fonte

### Primeiro programa em Go

```
$ mkdir ~\go\src\hello
```

*Arquivo*: ~\go\src\hello\hello.go

```
package main

import "fmt"

func main() {
    fmt.Printf("hello, world\n")
}
```

### Compilação

```
$ cd ~\go\src\hello
$ go build hello.go
```

Execução:

```
$ ~\go\src\hello\hello
```

### Compilação e execução

```
$ cd ~\go\src\hello
$ go run hello.go
```

# 2 - Variáveis

## Tipos de variáveis

*Arquivo*: ~\go\src\hello\hello.go
```
package main

import "fmt"

func main() {

    var nome string = "Lucca"
    var versao float32 = 1.2
    var idade int = 13

    fmt.Println("Olá", nome, ". A sua idade é ", idade, "anos.")
    fmt.Println("A versão do seu app é", versao)

}
```

Compilação e execução:

```
$ cd ~\go\src\hello
$ go run hello.go
Olá Lucca . A sua idade é  13 anos.
A versão do seu app é 1.2
```

### Variável declarada, mas não utilizada

No Go, não podermos declarar uma variável e não utilizá-la. Ocorre erro de compilação.


## Inferência de Tipos

É possível omitir o tipo da variável no momento da sua declaração, pois o Go consegue fazer a inferência do tipo:

Exemplo - Antes:
```
var nome string = "Lucca"
```

Exemplo - Depois:
```
var nome = "Lucca"
```

Funciona bem com os tipos `string`, `int` e `boolean`, mas não é aconselhável para tipos `float`, pois não sabemos qual das versões do `float` o Go utilizará em runtime.

*Arquivo*: ~\go\src\hello\hello.go
```
package main

import (
    "fmt"
    "reflect"
)

func main() {

    var nome = "Lucca"
    var idade = 13
    var versao float32 = 1.2

    fmt.Println("Olá", nome, ". A sua idade é ", idade, "anos.")
    fmt.Println("A versão do seu app é", versao)

    fmt.Println("O tipo da variavel nome é", reflect.TypeOf(nome))
    fmt.Println("O tipo da variavel idade é", reflect.TypeOf(idade))

}
```

Compilação e execução:

```
$ cd ~\go\src\hello
$ go run hello.go
Olá Lucca . A sua idade é  13 anos.
A versão do seu app é 1.2
O tipo da variavel nome é string
O tipo da variavel idade é int
```

## Forma curta para declaração de variáveis

O Go oferece o operador de atribuição `:=` que informa que estamos declarando uma variável e atribuindo um valor a ela (fazendo também a inferência de tipo). Isso possibilita a remoção da palavra-chave `var`.

*Arquivo*: ~\go\src\hello\hello.go
```
package main

import (
    "fmt"
)

func main() {

    nome := "Lucca"
    idade := 13
    versao := 1.2

    fmt.Println("Olá", nome, ". A sua idade é ", idade, "anos.")
    fmt.Println("A versão do seu app é", versao)

}
```

## Scan e Scanf

*Arquivo*: ~\go\src\hello\hello.go
```
package main

import "fmt"

func main() {

    nome := "Lucca"
    versao := 1.2

    fmt.Println("Olá, sr(a).", nome)
    fmt.Println("A versão do seu app é", versao)

    fmt.Println("1 - Iniciar monitoração")
    fmt.Println("2 - Exibir Logs")
    fmt.Println("0 - Sair")

    var comando int
    fmt.Scan(&comando)

    fmt.Println("O valor da variável comando é:", comando)

}
```

Compilação e execução:

```
$ go run hello.go 
Olá, sr(a). Lucca
A versão do seu app é 1.2
1 - Iniciar monitoração
2 - Exibir Logs
0 - Sair
2
O valor da variável comando é: 2
```

Inicialmente usamos o trecho:

```
    var comando int
    fmt.Scanf("%d", &comando)
```

Com a função Scanf usamos o parâmetro "%d", que significa que esperamos receber um número inteiro, e o ponteiro da variável "comando" que receberá o valor digitado pelo usuário.

Com a função Scan não há essa necessidade, pois é feita a inferência do tipo da variável.


# 3 - Controle de Fluxo e Funções

## if

*Arquivo*: ~\go\src\hello\hello.go
```
package main

import "fmt"

func main() {

    nome := "Lucca"
    versao := 1.2

    fmt.Println("Olá, sr(a).", nome)
    fmt.Println("A versão do seu app é", versao)

    fmt.Println("1 - Iniciar monitoração")
    fmt.Println("2 - Exibir Logs")
    fmt.Println("0 - Sair")

    var comando int
    fmt.Scan(&comando)

    if comando == 1 {
        fmt.Println("Monitorando...")
    } else if comando == 2 {
        fmt.Println("Exibindo Logs...")
    } else if comando == 0 {
        fmt.Println("Saindo do programa...")
    } else {
        fmt.Println("Não conheço este comando")
    }

}
```

Compilação e execução:
```
$ go run hello.go 
Olá, sr(a). Lucca
A versão do seu app é 1.2
1 - Iniciar monitoração
2 - Exibir Logs
0 - Sair
3
Não conheço este comando
```


## switch

```
    switch comando {
    case 1:
        fmt.Println("Monitorando...")
    case 2:
        fmt.Println("Exibindo Logs...")
    case 0:
        fmt.Println("Saindo do programa...")
    default:
        fmt.Println("Não conheço este comando")
    }
```


## funções

*Arquivo*: ~\go\src\hello\hello.go
```
package main

import (
    "fmt"
    "os"
)

func main() {

    exibeIntroducao()

    exibeMenu()

    comando := leComando()

    if comando == 1 {
        fmt.Println("Monitorando...")
    } else if comando == 2 {
        fmt.Println("Exibindo Logs...")
    } else if comando == 0 {
        fmt.Println("Saindo do programa...")
        os.Exit(0)
    } else {
        fmt.Println("Não conheço este comando")
        os.Exit(-1)
    }

}

func exibeIntroducao() {
    nome := "Lucca"
    versao := 1.2

    fmt.Println("Olá, sr(a).", nome)
    fmt.Println("A versão do seu app é", versao)
}

func leComando() int {
    var comandoLido int
    fmt.Scan(&comandoLido)
    fmt.Println("O comando escolhido foi", comandoLido)

    return comandoLido
}

func exibeMenu() {
    fmt.Println("1 - Iniciar monitoração")
    fmt.Println("2 - Exibir Logs")
    fmt.Println("0 - Sair")
}
```

# 4 - Requisições Web




```
```

```
```

```
```

```
```

```
```

```
```
