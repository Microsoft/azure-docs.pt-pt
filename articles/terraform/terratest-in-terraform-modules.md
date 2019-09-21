---
title: Testar módulos do Terraform no Azure usando o Terratest
description: Saiba como utilizar o Terratest para testar os seus módulos do Terraform.
services: terraform
ms.service: azure
keywords: terraform, devops, conta de armazenamento, azure, terratest, teste de unidades, teste de integração
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 637bb01bff625989e392d5d711ebd5cdef5c0e09
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169641"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Testar módulos do Terraform no Azure usando o Terratest

> [!NOTE]
> O código de exemplo neste artigo não funciona com a versão 0,12 (e superior).

Você pode usar os módulos do Azure Terraform para criar componentes reutilizáveis, combináveis e que podem ser testados. Os módulos Terraform incorporam o encapsulamento que é útil para implementar a infraestrutura como processos de código.

É importante implementar o controle de qualidade ao criar módulos Terraform. Infelizmente, a documentação limitada está disponível para explicar como criar testes de unidade e testes de integração nos módulos do Terraform. Este tutorial apresenta uma infraestrutura de teste e práticas recomendadas que adotamos quando criamos os [módulos Terraform do Azure](https://registry.terraform.io/browse?provider=azurerm).

Examinamos todas as infra-estruturas de teste mais populares e escolhemos o [Terratest](https://github.com/gruntwork-io/terratest) para usar para testar nossos módulos Terraform. O Terratest é implementado como uma biblioteca de Go. O Terratest fornece uma coleção de funções auxiliares e padrões para tarefas comuns de teste de infraestrutura, como fazer solicitações HTTP e usar o SSH para acessar uma máquina virtual específica. A lista a seguir descreve algumas das principais vantagens do uso do Terratest:

- **Ele fornece auxílios convenientes para verificar a infraestrutura**. Esta funcionalidade é útil para quando quer verificar a infraestrutura real no ambiente real.
- **A estrutura de pastas é claramente organizada**. Os casos de teste são organizados claramente e seguem a [estrutura de pastas do módulo Terraform padrão](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Todos os casos de teste são escritos em go**. A maioria dos desenvolvedores que usam o Terraform são desenvolvedores. Se você for um desenvolvedor Go, não precisará aprender outra linguagem de programação para usar o Terratest. Além disso, as únicas dependências necessárias para executar casos de teste em Terratest são go e Terraform.
- **A infraestrutura é altamente extensível**. Você pode estender funções adicionais na parte superior do Terratest, incluindo recursos específicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo prático é independente de plataforma. Você pode executar os exemplos de código que usamos neste artigo no Windows, Linux ou MacOS. 

Antes de começar, instale o seguinte software:

- **Linguagem de programação go**: Os casos de teste do Terraform são escritos em [go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) é uma ferramenta de gestão de dependências para Go.
- **CLI do Azure**: O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) é uma ferramenta de linha de comando que você pode usar para gerenciar recursos do Azure. (O Terraform dá suporte à autenticação no Azure por meio de uma entidade de serviço ou [por meio do CLI do Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **Mage**: Usamos o [executável do Mage](https://github.com/magefile/mage/releases) para mostrar como simplificar a execução de casos Terratest. 

## <a name="create-a-static-webpage-module"></a>Criar um módulo de página Web estática

Neste tutorial, você cria um módulo Terraform que provisiona uma página da Web estática carregando um único arquivo HTML para um blob de armazenamento do Azure. Este módulo fornece aos usuários do mundo todo o acesso à página da Web por meio de uma URL que o módulo retorna.

> [!NOTE]
> Crie todos os arquivos descritos nesta seção em seu local [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) .

Primeiro, crie uma nova pasta chamada `staticwebpage` na pasta GoPath `src` . A estrutura geral de pastas deste tutorial é mostrada no exemplo a seguir. Os arquivos marcados com um `(*)` asterisco são o principal foco nesta seção.

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

O módulo da página da Web estática aceita três entradas. As entradas são declaradas em `./variables.tf`:

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

Como mencionamos anteriormente neste artigo, esse módulo também gera uma URL que é declarada `./outputs.tf`em:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

A lógica principal do módulo provisiona quatro recursos:
- **grupo de recursos**: O nome do grupo de recursos é a `website_name` entrada acrescentada por `-staging-rg`.
- **conta de armazenamento**: O nome da conta de armazenamento é a `website_name` entrada acrescentada por `data001`. Para aderir às limitações de nome da conta de armazenamento, o módulo remove todos os caracteres especiais e usa letras minúsculas em todo o nome da conta de armazenamento.
- **contêiner de nome fixo**: O contêiner é nomeado `wwwroot` e é criado na conta de armazenamento.
- **arquivo HTML único**: O arquivo HTML é lido a partir `html_path` da entrada e carregado `wwwroot/index.html`para.

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

O Terratest foi projetado para testes de integração. Para essa finalidade, o Terratest provisiona recursos reais em um ambiente real. Às vezes, os trabalhos de teste de integração podem se tornar excepcionalmente grandes, especialmente quando você tem um grande número de recursos para provisionar. A lógica que converte os nomes de conta de armazenamento que nos referimos na seção anterior é um bom exemplo. 

Mas, na verdade, não precisamos provisionar nenhum recurso. Queremos apenas garantir que a lógica de conversão de nomenclatura esteja correta. Graças à flexibilidade do Terratest, podemos usar testes de unidade. Os testes de unidade são casos de teste em execução local (embora o acesso à Internet seja necessário). Os casos de teste `terraform init` de `terraform plan` unidade executam comandos e para `terraform plan` analisar a saída de e procurar os valores de atributo a serem comparados.

O restante desta seção descreve como usamos Terratest para implementar um teste de unidade para garantir que a lógica usada para converter nomes de conta de armazenamento esteja correta. Estamos interessados apenas nos arquivos marcados com um asterisco `(*)`.

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

Primeiro, usamos um arquivo HTML vazio chamado `./test/fixtures/storage-account-name/empty.html` como um espaço reservado.

O arquivo `./test/fixtures/storage-account-name/main.tf` é o quadro de caso de teste. Ele aceita uma entrada, `website_name`, que também é a entrada dos testes de unidade. A lógica é mostrada aqui:

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

Os desenvolvedores provavelmente perceberão que o teste de unidade corresponde à assinatura de uma função de teste do Go clássico aceitando um `*testing.T`argumento do tipo.

No corpo do teste de unidade, temos um total de cinco casos definidos em variável `testCases` (`key` como entrada e `value` saída esperada). Para cada caso de teste de unidade, primeiro `terraform init` executamos e direcionamos a pasta`./test/fixtures/storage-account-name/`de acessório de teste (). 

Em seguida, `terraform plan` um comando que usa entrada de caso de teste específico (dê uma `website_name` olhada na `tfOptions`definição em) salva o `./test/fixtures/storage-account-name/terraform.tfplan` resultado em (não listado na estrutura geral de pastas).

Esse arquivo de resultado é analisado para uma estrutura legível por código usando o analisador de plano oficial do Terraform.

Agora, procuramos os atributos nos quais estamos interessados (nesse caso, o `name` `azurerm_storage_account`do) e comparamos os resultados com a saída esperada:

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

Para executar os testes de unidade, conclua as seguintes etapas na linha de comando:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

O resultado do teste go tradicional retorna em cerca de um minuto.

### <a name="integration-test"></a>Teste de integração

Em contraste com os testes de unidade, os testes de integração devem provisionar recursos para um ambiente real para uma perspectiva de ponta a ponta. O Terratest faz um bom trabalho com esse tipo de tarefa. 

As práticas recomendadas para os módulos Terraform `examples` incluem a instalação da pasta. A `examples` pasta contém alguns exemplos de ponta a ponta. Para evitar trabalhar com dados reais, por que não testar esses exemplos como testes de integração? Nesta seção, nos concentramos nos três arquivos marcados com um asterisco `(*)` na seguinte estrutura de pastas:

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

Vamos começar com os exemplos. Uma nova pasta de exemplo `hello-world/` chamada é criada `./examples/` na pasta. Aqui, fornecemos uma página HTML simples a ser carregada: `./examples/hello-world/index.html`.

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

O exemplo `./examples/hello-world/main.tf` de Terraform é semelhante ao mostrado no teste de unidade. Há uma diferença significativa: o exemplo também imprime a URL do HTML carregado como uma página da Web chamada `homepage`.

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

Usamos as funções Terratest e Classic Test do Go novamente no arquivo `./test/hello_world_example_test.go`de teste de integração.

Diferentemente dos testes de unidade, os testes de integração criam recursos reais no Azure. É por isso que você precisa ter cuidado para evitar conflitos de nomenclatura. (Preste atenção especial a alguns nomes globalmente exclusivos, como nomes de conta de armazenamento.) Portanto, a primeira etapa da lógica de teste é gerar um aleatório `websiteName` usando a `UniqueId()` função fornecida por Terratest. Essa função gera um nome aleatório que tem letras minúsculas, letras maiúsculas ou números. `tfOptions`faz todos os comandos Terraform que se `./examples/hello-world/` destinam à pasta. Ele também garante que `website_name` está definido para o `websiteName`aleatório.

Em seguida, são executados `terraform init`, `terraform apply` e `terraform output`, um a um. Usamos outra função auxiliar, `HttpGetWithCustomValidation()`, que é fornecida pelo Terratest. Usamos a função auxiliar para garantir que o HTML seja carregado na URL de saída `homepage` retornada pelo. `terraform output` Comparamos o código de status http `200` Get com e procuramos algumas palavras-chave no conteúdo HTML. Por fim, é “prometido” que se tira partido da funcionalidade `defer` do Go para executar `terraform destroy`.

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

Para executar os testes de integração, conclua as seguintes etapas na linha de comando:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

O resultado do teste go tradicional retorna em cerca de dois minutos. Você também pode executar os testes de unidade e testes de integração executando estes comandos:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Os testes de integração demoram muito mais do que os testes de unidade (dois minutos para um caso de integração em comparação com um minuto em cinco casos de unidade). Mas é sua decisão se você deve usar testes de unidade ou testes de integração em um cenário. Normalmente, preferimos usar testes de unidade para lógica complexa usando as funções da HCL Terraform. Geralmente, usamos testes de integração para a perspectiva de ponta a ponta de um usuário.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Utilizar mage para simplificar a execução de casos do Terratest 

Executar casos de teste no Azure Cloud Shell não é uma tarefa fácil. Você precisa ir para diretórios diferentes e executar comandos diferentes. Para evitar o uso de Cloud Shell, apresentamos o sistema de compilação em nosso projeto. Nesta seção, usamos um sistema de compilação Go, Mage, para o trabalho.

A única coisa exigida pelo Mage `magefile.go` está no diretório raiz do seu projeto (marcado `(+)` com no exemplo a seguir):

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

Aqui está um exemplo de `./magefile.go`. Neste script de Build, escrito em go, implementamos cinco etapas de compilação:
- `Clean`: A etapa remove todos os arquivos gerados e temporários gerados durante as execuções de teste.
- `Format`: A etapa é `terraform fmt` executada `go fmt` e para formatar sua base de código.
- `Unit`: A etapa executa todos os testes de unidade (usando a Convenção `TestUT_*`de nome de função) `./test/` na pasta.
- `Integration`: A etapa é semelhante a `Unit`, mas em vez de testes de unidade, ela executa testes de`TestIT_*`integração ().
- `Full`: A etapa executa `Clean`, `Format`, `Unit`e `Integration` em sequência.

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

Você pode usar os comandos a seguir para executar um conjunto de testes completo. O código é semelhante às etapas em execução usadas em uma seção anterior. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Você pode substituir a última linha de comando por etapas adicionais do Mage. Por exemplo, você pode usar `mage unit` ou `mage clean`. É uma boa ideia inserir `dep` comandos e `az login` no magefile. Não mostramos o código aqui. 

Com o Mage, você também pode compartilhar as etapas usando o sistema do pacote go. Nesse caso, você pode simplificar o magefiles em todos os seus módulos referenciando apenas uma implementação comum e declarando dependências (`mg.Deps()`).

**Adicional Definir variáveis de ambiente de entidade de serviço para executar testes de aceitação**
 
Em vez de `az login` executar antes dos testes, você pode concluir a autenticação do Azure definindo as variáveis de ambiente da entidade de serviço. Terraform publica uma [lista de nomes de variáveis de ambiente](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Só são necessárias as quatro primeiras variáveis de ambiente.) O Terraform também publica instruções detalhadas que explicam como [obter o valor dessas variáveis de ambiente](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre Terratest, consulte a [página do GitHub do Terratest](https://github.com/gruntwork-io/terratest).
* Para obter informações sobre o Mage, consulte a [página do GitHub do Mage](https://github.com/magefile/mage) e o site do [Mage](https://magefile.org/).
