---
title: Tutorial - Módulos Terraforme de teste em Azure usando terratest
description: Saiba como utilizar o Terratest para testar os seus módulos do Terraform.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 687a793af2b9b75efe463b042d121c32f18974d6
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370802"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Tutorial: Testar módulos terraformes em Azure usando terratest

> [!NOTE]
> O código da amostra neste artigo não funciona com a versão 0.12 (e maior).

Pode utilizar módulos Terraform Azure para criar componentes reutilizáveis, compizáveis e testáveis. Os módulos terraformeincorporam encapsulação que é útil na implementação da infraestrutura como processos de código.

É importante implementar garantias de qualidade quando se criam módulos Terraform. Infelizmente, está disponível documentação limitada para explicar como autor esquestão de testes de unidade e testes de integração em módulos Terraform. Este tutorial introduz uma infraestrutura de teste e as melhores práticas que adotámos quando construímos os [nossos módulos Azure Terraform.](https://registry.terraform.io/browse?provider=azurerm)

Analisámos todas as infraestruturas de testes mais populares e escolhemos a [Terratest](https://github.com/gruntwork-io/terratest) para testar os nossos módulos Terraform. O Terratest é implementado como uma biblioteca de Go. A Terratest fornece uma coleção de funções e padrões de ajudante para tarefas comuns de teste de infraestrutura, como fazer pedidos HTTP e usar sSH para aceder a uma máquina virtual específica. A lista seguinte descreve algumas das principais vantagens da utilização da Terratest:

- **Fornece ajudantes convenientes para verificar infraestruturas.** Esta funcionalidade é útil para quando quer verificar a infraestrutura real no ambiente real.
- **A estrutura da pasta está claramente organizada.** Os seus casos de teste são organizados de forma clara e seguem a estrutura padrão da pasta do [módulo Terraform](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Todos os casos de teste estão escritos em Go**. A maioria dos desenvolvedores que usam terraform são desenvolvedores Go. Se és um desenvolvedor de Go, não tens de aprender outra linguagem de programação para usar o Terratest. Além disso, as únicas dependências que são necessárias para que você execute casos de teste em Terratest são Go e Terraform.
- **A infraestrutura é altamente extensível.** Pode estender funções adicionais em cima da Terratest, incluindo funcionalidades específicas do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo prático é independente da plataforma. Pode executar os exemplos de código que utilizamos neste artigo no Windows, Linux ou MacOS. 

Antes de começar, instale o seguinte software:

- **Linguagem de programação go**: Os casos de teste terraforme estão escritos em [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) é uma ferramenta de gestão de dependências para Go.
- **Azure CLI**: O [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) é uma ferramenta de linha de comando que pode utilizar para gerir os recursos do Azure. (Terraform suporta autenticação no Azure através de um diretor de serviço ou [através do Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: Usamos o [mage executável para](https://github.com/magefile/mage/releases) lhe mostrar como simplificar casos de Terratest. 

## <a name="create-a-static-webpage-module"></a>Criar um módulo de página Web estática

Neste tutorial, você cria um módulo Terraform que disponibiliza uma página estátática através do upload de um único ficheiro HTML para uma bolha de Armazenamento Azure. Este módulo dá aos utilizadores de todo o mundo acesso à página web através de um URL que o módulo retorna.

> [!NOTE]
> Crie todos os ficheiros descritos nesta secção sob a sua localização [GOPATH.](https://github.com/golang/go/wiki/SettingGOPATH)

Em primeiro lugar, crie uma nova pasta chamada `staticwebpage` sob a pasta GoPath `src`. A estrutura global da pasta deste tutorial é mostrada no seguinte exemplo. Os ficheiros marcados com um asterisco `(*)` são o principal foco nesta secção.

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

O módulo estático da página web aceita três inputs. As inputs são declaradas em `./variables.tf`:

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

Como mencionamos anteriormente no artigo, este módulo também produz um URL que é declarado em `./outputs.tf`:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

A principal lógica do módulo disponibiliza quatro recursos:
- **grupo**de recursos : O nome do grupo de recursos é a entrada `website_name` anexada por `-staging-rg`.
- **conta de armazenamento**: O nome da conta de armazenamento é a entrada `website_name` anexada por `data001`. Para aderir às limitações de nome da conta de armazenamento, o módulo remove todos os caracteres especiais e utiliza letras minúsculas em todo o nome da conta de armazenamento.
- **recipiente de nome fixo**: O recipiente tem o nome `wwwroot` e é criado na conta de armazenamento.
- **ficheiro HTML único**: O ficheiro HTML é lido a partir da entrada `html_path` e enviado para `wwwroot/index.html`.

A lógica do módulo da página Web estática é implementado em `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Teste de unidades

Terratest é projetado para testes de integração. Para o efeito, a Terratest disponibiliza recursos reais num ambiente real. Por vezes, os postos de trabalho em testes de integração podem tornar-se excepcionalmente grandes, especialmente quando se dispõe de um grande número de recursos para fornecer. A lógica que converte nomes de contas de armazenamento a que nos referimos na secção anterior é um bom exemplo. 

Mas não precisamos de fornecer recursos. Só queremos ter a certeza de que a lógica de conversão de nomeação está correta. Graças à flexibilidade da Terratest, podemos usar testes unitários. Os testes unitários são casos locais de teste de execução (embora seja necessário o acesso à Internet). Os casos de teste da unidade executam `terraform init` e `terraform plan` comandos para analisar a saída de `terraform plan` e procurar os valores do atributo para comparar.

O resto desta secção descreve como usamos a Terratest para implementar um teste de unidade para garantir que a lógica usada para converter nomes de conta de armazenamento está correta. Estamos interessados apenas nos ficheiros marcados com um asterisco `(*)`.

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

Primeiro, usamos um ficheiro HTML vazio chamado `./test/fixtures/storage-account-name/empty.html` como espaço reservado.

O ficheiro `./test/fixtures/storage-account-name/main.tf` é a moldura do teste. Aceita uma entrada, `website_name`, que é também a entrada dos testes unitários. A lógica é mostrada aqui:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

O principal componente é a implementação dos testes unitários em `./test/storage_account_name_unit_test.go`.

Os desenvolvedores de go provavelmente notarão que o teste da unidade corresponde à assinatura de uma função de teste clássica go aceitando um argumento de tipo `*testing.T`.

No corpo do teste unitário, temos um total de cinco casos que são definidos em `testCases` variável (`key` como entrada, e `value` como saída esperada). Para cada estojo de teste de unidade, executamos primeiro `terraform init` e visamos a pasta de fixação de ensaio (`./test/fixtures/storage-account-name/`). 

Em seguida, um comando `terraform plan` que utiliza uma entrada específica de casos de teste (veja a definição `website_name` em `tfOptions`) guarda o resultado para `./test/fixtures/storage-account-name/terraform.tfplan` (não listado na estrutura global da pasta).

Este ficheiro de resultados é analisado para uma estrutura legível por código utilizando o analisador oficial do plano Terraform.

Agora, procuramos os atributos que nos interessam (neste caso, o `name` do `azurerm_storage_account`) e comparamos os resultados com a saída esperada:

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
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

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

Para executar os testes da unidade, complete os seguintes passos na linha de comando:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

O resultado tradicional do teste go regressa em cerca de um minuto.

### <a name="integration-test"></a>Teste de integração

Em contraste com os testes unitários, os testes de integração devem fornecer recursos para um ambiente real para uma perspetiva de ponta a ponta. Terratest faz um bom trabalho com este tipo de tarefa. 

As melhores práticas para os módulos Terraform incluem a instalação da pasta `examples`. A pasta `examples` contém algumas amostras de ponta a ponta. Para evitar trabalhar com dados reais, por que não testar essas amostras como testes de integração? Nesta secção, focamo-nos nos três ficheiros que são marcados com um asterisco `(*)` na estrutura da seguinte pasta:

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

Vamos começar com as amostras. Uma nova pasta de amostra chamada `hello-world/` é criada na pasta `./examples/`. Aqui, fornecemos uma simples página HTML a ser carregada: `./examples/hello-world/index.html`.

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

A amostra Terraforme `./examples/hello-world/main.tf` é semelhante à mostrada no teste unitário. Há uma diferença significativa: a amostra também imprime o URL do HTML carregado como uma página web chamada `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

Usamos funções de teste Terratest e Go clássicas novamente no arquivo de teste de integração `./test/hello_world_example_test.go`.

Ao contrário dos testes unitários, os testes de integração criam recursos reais em Azure. É por isso que tens de ter cuidado para evitar nomear conflitos. (Preste especial atenção a alguns nomes globalmente únicos, como nomes de contas de armazenamento.) Portanto, o primeiro passo da lógica de teste é gerar uma `websiteName` aleatória utilizando a função `UniqueId()` fornecida pela Terratest. Esta função gera um nome aleatório que tem letras minúsculas, letras maiúsculas ou números. `tfOptions` faz todos os comandos Terraform que visam a pasta `./examples/hello-world/`. Certifica-se igualmente de que `website_name` se fixe para a `websiteName`aleatória .

Em seguida, são executados `terraform init`, `terraform apply` e `terraform output`, um a um. Utilizamos outra função de ajudante, `HttpGetWithCustomValidation()`, que é fornecida pela Terratest. Utilizamos a função de ajudante para garantir que o HTML é carregado para o URL `homepage` de saída que é devolvido por `terraform output`. Comparamos o código de estado HTTP GET com `200` e procuramos algumas palavras-chave no conteúdo HTML. Por fim, é “prometido” que se tira partido da funcionalidade `terraform destroy` do Go para executar `defer`.

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

Para executar os testes de integração, complete os seguintes passos na linha de comando:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

O resultado tradicional do teste go regressa em cerca de dois minutos. Também pode executar testes de unidade e testes de integração executando estes comandos:

```shell
go fmt
go test
```

Os testes de integração demoram muito mais tempo do que os testes unitários (dois minutos para um caso de integração em comparação com um minuto para cinco casos unitários). Mas a decisão é tua se usares testes unitários ou testes de integração num cenário. Normalmente, preferimos usar testes unitários para lógica complexa utilizando funções De HCL Terraform. Normalmente utilizamos testes de integração para a perspetiva de ponta a ponta de um utilizador.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Utilizar mage para simplificar a execução de casos do Terratest 
Executar casos de teste em Azure Cloud Shell requer a execução de diferentes comandos em vários diretórios. Para tornar este processo mais eficiente, introduzimos o sistema de construção no nosso projeto. Nesta secção, usamos um sistema de construção Go, mage, para o trabalho.

A única coisa exigida por mage é `magefile.go` no diretório raiz do seu projeto (marcado com `(+)` no exemplo seguinte):

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

Aqui está um exemplo de `./magefile.go`. Neste roteiro de construção, escrito em Go, implementamos cinco passos de construção:
- `Clean`: O passo remove todos os ficheiros gerados e temporários que são gerados durante as execuções de teste.
- `Format`: O passo passa `terraform fmt` e `go fmt` para formatar a sua base de código.
- `Unit`: O passo executa todos os ensaios unitários (utilizando a convenção de nome de função `TestUT_*`) sob a pasta `./test/`.
- `Integration`: O passo é semelhante ao `Unit`, mas em vez de testes unitários, executa testes de integração (`TestIT_*`).
- `Full`: O passo corre `Clean`, `Format`, `Unit`e `Integration` em sequência.

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

Pode utilizar os seguintes comandos para executar uma suíte de teste completa. O código é semelhante aos passos de execução que usamos numa secção anterior. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

Pode substituir a última linha de comando por passos adicionais de mage. Por exemplo, pode utilizar `mage unit` ou `mage clean`. É uma boa ideia incorporar comandos `dep` e `az login` no magefile. Não mostramos o código aqui. 

Com mage, você também pode partilhar os passos usando o sistema de pacotes Go. Nesse caso, pode simplificar os ficheiros magefiles em todos os seus módulos, referindo apenas uma implementação comum e declarando dependências (`mg.Deps()`).

**Opcional: Definir variáveis ambientais principais do serviço para executar testes de aceitação**
 
Em vez de executar `az login` antes dos testes, pode concluir a autenticação Azure definindo as variáveis ambientais principais do serviço. Terraform publica uma [lista de nomes variáveis ambientais.](https://www.terraform.io/docs/providers/azurerm/index.html#testing) (Apenas são necessárias as quatro primeiras variáveis ambientais.) A Terraform também publica instruções detalhadas que explicam como [obter o valor destas variáveis ambientais.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Página Terratest GitHub.](https://github.com/gruntwork-io/terratest)