---
title: Testar os módulos do Terraform no Azure utilizando Terratest
description: Saiba como utilizar o Terratest para testar os seus módulos do Terraform.
services: terraform
ms.service: azure
keywords: terraform, devops, conta de armazenamento, azure, terratest, teste de unidades, teste de integração
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 936fe347fb4f9d5e27e005099725674341960457
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773321"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Testar os módulos do Terraform no Azure utilizando Terratest

Pode utilizar módulos do Terraform do Azure para criar reutilizáveis e compostas e componentes que podem ser testados. Módulos do Terraform incorporam encapsulamento, que é útil para implementar a infraestrutura como processos de código.

É importante implementar o controle de qualidade ao criar módulos do Terraform. Infelizmente, a documentação limitada está disponível para explicar como criar testes de unidade e testes de integração em módulos do Terraform. Este tutorial apresenta uma infraestrutura de teste e práticas recomendadas que adotámos quando criamos nosso [Azure Terraform modules](https://registry.terraform.io/browse?provider=azurerm).

Vimos que, ao teste de infraestruturas e escolha o máximo partido populares [Terratest](https://github.com/gruntwork-io/terratest) a utilizar para testar os nossos módulos do Terraform. O Terratest é implementado como uma biblioteca de Go. Terratest fornece uma coleção de funções auxiliares e padrões para a infraestrutura comuns de tarefas, como efetuar pedidos HTTP e através de SSH para aceder a uma máquina virtual específica de teste. A lista seguinte descreve algumas das principais vantagens da utilização de Terratest:

- **Ele fornece auxiliares convenientes para verificar a infraestrutura**. Esta funcionalidade é útil para quando quer verificar a infraestrutura real no ambiente real.
- **A estrutura de pastas está organizada claramente**. Seus casos de teste são organizados claramente e siga os [padrão estrutura de pasta do módulo de Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Todos os casos de teste são escritos em Go**. A maioria dos desenvolvedores que usam o Terraform são programadores Go. Se for um desenvolvedor de Go, não precisa de saber outra linguagem de programação para utilizar Terratest. Além disso, as dependências apenas que são necessárias para a execução de casos de teste no Terratest são Go e o Terraform.
- **A infraestrutura é altamente extensível**. Pode expandir as funções adicionais por cima Terratest, incluindo recursos específicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo prático é independente de plataforma. Pode executar os exemplos de código que usamos neste artigo no Windows, Linux ou MacOS. 

Antes de começar, instale o seguinte software:

- **Aceda a linguagem de programação**: Casos de teste do Terraform são escritos em [ir](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) é uma ferramenta de gestão de dependências para Go.
- **CLI do Azure**: O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) é uma ferramenta da linha de comandos, pode utilizar para gerir recursos do Azure. (Terraform suporta a autenticação no Azure através de um principal de serviço ou [através da CLI do Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: Vamos utilizar o [mage executável](https://github.com/magefile/mage/releases) para mostrar a como simplificar casos de Terratest em execução. 

## <a name="create-a-static-webpage-module"></a>Criar um módulo de página Web estática

Neste tutorial, vai criar um módulo de Terraform que Aprovisiona uma página Web estática através do carregamento de um único arquivo HTML para um blob de armazenamento do Azure. Este módulo apresenta os utilizadores de todo o acesso do mundo para a página Web através de um URL que o módulo retorna.

> [!NOTE]
> Criar todos os ficheiros que são descritos nesta secção em sua [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) localização.

Primeiro, crie uma nova pasta chamada `staticwebpage` sob o GoPath `src` pasta. A estrutura geral das pastas deste tutorial é mostrada no exemplo a seguir. Ficheiros marcados com asterisco `(*)` são o principal foco nesta secção.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

O módulo de página Web estática aceita três entradas. As entradas são declaradas nos `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Como mencionado anteriormente neste artigo, este módulo também produz um URL que está declarado no `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

A lógica principal do módulo Aprovisiona quatro recursos:
- **Grupo de recursos**: O nome do grupo de recursos é o `website_name` entrada anexada por `-staging-rg`.
- **Conta de armazenamento**: O nome da conta de armazenamento é o `website_name` entrada anexada por `data001`. A cumprir as limitações de nome da conta de armazenamento, o módulo remove todos os carateres especiais e utiliza as letras minúsculas no nome da conta de armazenamento completo.
- **foi corrigido o contentor de nome**: O contentor com o nome `wwwroot` e é criado na conta de armazenamento.
- **arquivo único do HTML**: O arquivo HTML é lidos a partir da `html_path` de entrada e carregou para `wwwroot/index.html`.

A lógica do módulo da página Web estática é implementado em `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Teste de unidades

Terratest destina-se a testes de integração. Para essa finalidade, Terratest Aprovisiona recursos real num ambiente real. Às vezes, tarefas de teste de integração podem se tornar extremamente grandes, especialmente quando tem um grande número de recursos para aprovisionar. A lógica que converte os nomes de contas de armazenamento que fazemos referência a na secção anterior é um bom exemplo. 

No entanto, não precisamos aprovisionar todos os recursos. Apenas queremos certificar-se de que a lógica de conversão de nomenclatura está correta. Graças a flexibilidade do Terratest, podemos usar testes de unidade. Testes de unidade são locais em execução casos de teste (embora o acesso à internet é necessário). Executar casos de teste de unidade `terraform init` e `terraform plan` comandos para analisar a saída de `terraform plan` e procure os valores de atributo comparar.

O resto desta secção descreve como utilizamos Terratest para implementar um teste de unidade para se certificar de que a lógica usada para converter os nomes de conta de armazenamento está correta. Estamos interessados apenas nos arquivos marcados com asterisco `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Em primeiro lugar, vamos utilizar um ficheiro HTML vazio com o nome `./test/fixtures/storage-account-name/empty.html` como um marcador de posição.

O ficheiro `./test/fixtures/storage-account-name/main.tf` é o quadro de caso de teste. Ele aceita uma entrada, `website_name`, que também é a entrada dos testes de unidade. A lógica é mostrada aqui:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

O principal componente é a implementação dos testes de unidade no `./test/storage_account_name_unit_test.go`.

Aceda a desenvolvedores provavelmente irão reparar que o teste de unidade corresponde à assinatura de uma função de teste de Go clássica ao aceitar um argumento de tipo `*testing.T`.

No corpo do teste de unidade, temos um total de cinco cenários que são definidas na variável `testCases` (`key` como entrada, e `value` como resultado esperado). Para cada caso de teste de unidade, executamos primeiro `terraform init` e a pasta de acessório de teste de destino (`./test/fixtures/storage-account-name/`). 

Em seguida, um `terraform plan` comando que utiliza a entrada de caso de teste específico (dar uma olhada a `website_name` definição na `tfOptions`) salva o resultado para `./test/fixtures/storage-account-name/terraform.tfplan` (não listado na estrutura da pasta geral).

Este ficheiro de resultado é analisado para uma estrutura de código legível ao utilizar o analisador de plano de Terraform oficial.

Agora, vamos ver para os atributos que estamos interessados (neste caso, o `name` do `azurerm_storage_account`) e compare os resultados com a saída esperada:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Para executar os testes de unidade, conclua os passos seguintes na linha de comando:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Devolve o resultado do teste Go tradicional em cerca de um minuto.

### <a name="integration-test"></a>Teste de integração

Em contraste com testes de unidade, testes de integração tem de aprovisionar recursos para um ambiente real de uma perspectiva de ponto-a-ponto. Terratest faz um bom trabalho com esse tipo de tarefa. 

Melhores práticas para os módulos do Terraform incluem a instalação a `examples` pasta. O `examples` pasta contém alguns exemplos de ponto-a-ponto. Para evitar a trabalhar com dados reais, por que não testar esses exemplos de como testes de integração? Nesta secção, vamos nos concentrar em três arquivos que estão marcados com um asterisco `(*)` na estrutura da pasta seguinte:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Vamos começar com os exemplos. Uma nova pasta de exemplo com o nome `hello-world/` é criado no `./examples/` pasta. Aqui, podemos fornecer uma página HTML simples para ser carregado: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

O exemplo de Terraform `./examples/hello-world/main.tf` é semelhante à mostrada no teste de unidade. Existe uma diferença significativa: o exemplo imprime também o URL do HTML carregado como uma página Web com o nome `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Usamos Terratest e funções de teste de Go clássicas novamente na integração testar arquivo `./test/hello_world_example_test.go`.

Ao contrário dos testes de unidade, testes de integração criam recursos reais no Azure. É por isso que precisa ter cuidado para evitar conflitos de nomenclatura. (Preste especial atenção a alguns nomes de globalmente exclusivos, como nomes de conta de armazenamento.) Por conseguinte, a primeira etapa da lógica do teste é gerar um aleatório `websiteName` utilizando o `UniqueId()` função fornecida pelo Terratest. Esta função gera um nome aleatório com letras minúsculas, letras maiúsculas ou números. `tfOptions` faz com que todos os comandos de Terraform destinados a `./examples/hello-world/` pasta. Ele também torna-se de que `website_name` é definido como o aleatório `websiteName`.

Em seguida, são executados `terraform init`, `terraform apply` e `terraform output`, um a um. Utilizamos outra função de auxiliar, `HttpGetWithCustomValidation()`, que é fornecido pelo Terratest. Usamos a função auxiliar para se certificar de que o HTML é carregado para a saída `homepage` URL que é devolvido pelo `terraform output`. Iremos comparar o código de estado HTTP GET com `200` e procurar por algumas palavras-chave no HTML de conteúdo. Por fim, é “prometido” que se tira partido da funcionalidade `defer` do Go para executar `terraform destroy`.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Para executar os testes de integração, conclua os passos seguintes na linha de comando:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Devolve o resultado do teste Go tradicional em cerca de dois minutos. Também pode executar testes de unidade e testes de integração ao executar estes comandos:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Testes de integração demoram muito mais do que testes de unidade (dois minutos para um caso de integração em comparação comparada um minuto para cinco casos de unidade). Mas é sua decisão para testes de unidade de utilização ou de testes de integração num cenário. Normalmente, preferimos usar testes de unidade para uma lógica complexa usando funções Terraform HCL. Normalmente, usamos testes de integração para o ponto de vista de ponto-a-ponto de um utilizador.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Utilizar mage para simplificar a execução de casos do Terratest 

A execução de casos de teste no Azure Cloud Shell, não é uma tarefa fácil. Terá de ir para diretórios diferentes e executar comandos diferentes. Para evitar a utilizar o Cloud Shell, apresentamos o sistema de compilação em nosso projeto. Nesta secção, vamos utilizar um sistema de compilação do Go, mage, para a tarefa.

É a única coisa que o exigido pelo mage `magefile.go` no diretório de raiz do seu projeto (marcado com `(+)` no exemplo a seguir):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Eis um exemplo de `./magefile.go`. Neste script de compilação, escrito em Go, implementamos cinco passos de compilação:
- `Clean`: O passo remove todos os ficheiros gerados e temporários, que são gerados durante execuções de teste.
- `Format`: O passo for executado `terraform fmt` e `go fmt` para formatar a sua base de código.
- `Unit`: O passo é executado todos os testes de unidade (usando a Convenção de nome de função `TestUT_*`) sob o `./test/` pasta.
- `Integration`: O passo é semelhante à `Unit`, mas em vez de testes de unidade, ele executa testes de integração (`TestIT_*`).
- `Full`: O passo for executado `Clean`, `Format`, `Unit`, e `Integration` na sequência.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Pode utilizar os seguintes comandos para executar um pacote de testes completa. O código é semelhante ao executar etapas usadas numa seção anterior. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Pode substituir a última linha de comando com os passos adicionais mage. Por exemplo, pode usar `mage unit` ou `mage clean`. É uma boa idéia incorporar `dep` comandos e `az login` no magefile. Não foi possível exibir o código aqui. 

Com mage, também, pode partilhar os passos, utilizando o sistema de pacote do Go. Nesse caso, pode simplificar a magefiles em todos os seus módulos por referenciar apenas uma implementação comum e declarar dependências (`mg.Deps()`).

**Opcional: Definir variáveis de ambiente de principal de serviço para executar testes de aceitação**
 
Em vez de executar `az login` antes de testes, pode concluir a autenticação do Azure ao definir as variáveis de ambiente de principal de serviço. Terraform publica um [lista de nomes de variáveis de ambiente](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Só são necessárias as quatro primeiras variáveis de ambiente.) Terraform também publica as instruções detalhadas que explicam como [obter o valor destas variáveis de ambiente](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre Terratest, consulte a [página do Terratest GitHub](https://github.com/gruntwork-io/terratest).
* Para obter informações sobre mage, consulte a [página do GitHub mage](https://github.com/magefile/mage) e o [mage site](https://magefile.org/).
