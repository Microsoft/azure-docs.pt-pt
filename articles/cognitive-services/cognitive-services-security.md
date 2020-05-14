---
title: Segurança
titleSuffix: Azure Cognitive Services
description: Conheça as várias considerações de segurança para o uso dos Serviços Cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: dapine
ms.openlocfilehash: fa0ad8c7f75a977e1a39ff6ffd6fee08d977f57a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202000"
---
# <a name="azure-cognitive-services-security"></a>Segurança dos Serviços Cognitivos Azure

A segurança deve ser considerada uma prioridade no desenvolvimento de todas e quaisquer aplicações. Com o aparecimento de aplicações ativadas pela inteligência artificial, a segurança é ainda mais importante. Neste artigo estão delineados vários aspetos da segurança dos Serviços Cognitivos Do Azure, tais como a utilização da segurança da camada de transporte, a autenticação, a configuração segura de dados sensíveis e o Bloqueio do Cliente para acesso aos dados dos clientes.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Todos os pontos finais dos Serviços Cognitivos expostos sobre HTTP aplicam TLS 1.2. Com um protocolo de segurança forçado, os consumidores que tentem chamar um ponto final dos Serviços Cognitivos devem aderir a estas orientações:

* O Sistema Operativo cliente (OS) precisa de suportar TLS 1.2
* O idioma (e plataforma) utilizado para fazer a chamada HTTP precisa especificar TLS 1.2 como parte do pedido
  * Dependendo do idioma e da plataforma, especificar TLS é feito implicitamente ou explicitamente

Para os utilizadores .NET, considere as <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">melhores práticas <span class="docon docon-navigate-external x-hidden-focus"></span> </a>de Segurança da Camada de Transporte .

## <a name="authentication"></a>Autenticação

Quando se discute a autenticação, existem vários equívocos comuns. A autenticação e a autorização são muitas vezes confundidas umas com as outras. A identidade é também um componente importante na segurança. Uma identidade é uma recolha de informação sobre um <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">diretor. <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Os fornecedores de identidade (IdP) fornecem identidades aos serviços de autenticação. A autenticação é o ato de verificar a identidade de um utilizador. A autorização é a especificação dos direitos de acesso e privilégios aos recursos para uma determinada identidade. Várias das ofertas dos Serviços Cognitivos, incluem controlo de acesso baseado em papéis (RBAC). O RBAC poderia ser usado para simplificar parte da cerimónia envolvida na gestão manual dos diretores. Para mais detalhes, consulte o [controlo de acesso baseado em papéis para os recursos Do Azure.](../role-based-access-control/overview.md)

Para obter mais informações sobre autenticação com chaves de subscrição, fichas de acesso e Diretório Ativo Azure (AAD), consulte <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">pedidos autenticados aos Serviços <span class="docon docon-navigate-external x-hidden-focus"></span> Cognitivos Azure</a>.

## <a name="environment-variables-and-application-configuration"></a>Variáveis ambientais e configuração de aplicação

As variáveis ambientais são pares de valor-nome, armazenados num ambiente específico. Uma alternativa mais segura para usar valores codificados para dados sensíveis é usar variáveis ambientais. Os valores codificados são inseguros e devem ser evitados.

> [!CAUTION]
> Não **not** utilize valores codificados para dados sensíveis, fazê-lo é uma vulnerabilidade de segurança importante.

> [!NOTE]
> Enquanto as variáveis ambientais são armazenadas em texto simples, são isoladas para um ambiente. Se um ambiente está comprometido, também as variáveis com o ambiente.

### <a name="set-environment-variable"></a>Definir variável ambiente

Para definir variáveis ambientais, utilize um dos seguintes comandos - onde a `ENVIRONMENT_VARIABLE_KEY` chave é nomeada e é o valor armazenado na `value` variável ambiental.

# <a name="command-line"></a>[Linha de Comando](#tab/command-line)

Criar e atribuir persistência de variável ambiental, dado o valor.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Num novo exemplo do **Comando Solicitação,** leia a variável ambiental.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Criar e atribuir persistência de variável ambiental, dado o valor.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Num novo caso do **Windows PowerShell,** leia a variável ambiental.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Criar e atribuir persistência de variável ambiental, dado o valor.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

Num novo exemplo da **Bash,** leia a variável ambiental.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Depois de definir uma variável ambiental, reinicie o seu ambiente de desenvolvimento integrado (IDE) para garantir que estão disponíveis novas variáveis ambientais.

### <a name="get-environment-variable"></a>Obter variável ambiental

Para obter uma variável ambiental, deve ser lido na memória. Dependendo da linguagem que está a usar, considere os seguintes códigos. Estes fragmentos de código demonstram como obter a variável ambiental dada a `ENVIRONMENT_VARIABLE_KEY` e atribuir a uma variável chamada `value` .

# <a name="c"></a>[C #](#tab/csharp)

Para mais informações, consulte <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Para mais informações, consulte <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Para mais informações, consulte <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Para mais informações, consulte <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Para mais informações, consulte <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Para mais informações, consulte <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Sistema de Proteção de Dados do Cliente

[O Bloqueio de Clientes para](../security/fundamentals/customer-lockbox-overview.md) o Microsoft Azure fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. É usado nos casos em que um engenheiro da Microsoft precisa de aceder aos dados dos clientes durante um pedido de suporte. Para obter informações sobre como os pedidos de bloqueio do cliente são iniciados, rastreados e armazenados para avaliações e auditorias posteriores, consulte o [Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md). 

O Bloqueio do Cliente está disponível para este Serviço Cognitivo:

* Tradutor

Para compreensão de idiomas, os engenheiros da Microsoft não acederão a quaisquer dados dos clientes no E0 SKU. Para solicitar a capacidade de utilização do E0 SKU, preencha e envie o Formulário de Pedido de [Serviço LUIS](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para voltar a ouvir o estado do seu pedido. Dependendo da procura, pode ser colocado numa fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para utilizar o E0 SKU com LUIS, terá de criar um novo recurso de Compreensão linguística a partir do portal Azure e selecionar E0 como O Nível de Preços. Os utilizadores não poderão fazer o upgrade do F0 para o novo E0 SKU.

O serviço de Fala não suporta atualmente o Bloqueio do Cliente. No entanto, os dados do cliente podem ser armazenados utilizando BYOS, permitindo-lhe obter controlos de dados semelhantes ao Bloqueio do [Cliente](../security/fundamentals/customer-lockbox-overview.md). Tenha em mente que os dados do serviço da Fala permanecem e são processados na região onde o recurso Speech foi criado. Isto aplica-se a quaisquer dados em repouso e dados em trânsito. Ao utilizar funcionalidades de personalização, como Discurso Personalizado e Voz Personalizada, todos os dados do cliente são transferidos, armazenados e processados na mesma região onde residem os seus recursos de serviço BYOS (se utilizados) e de serviço de Fala.

> [!IMPORTANT]
> A Microsoft **não** utiliza dados dos clientes para melhorar os seus modelos De Discurso. Além disso, se o registo de pontos finais for desativado e não forem utilizadas personalizações, então não são armazenados dados do cliente. 

## <a name="next-steps"></a>Próximos passos

* Explore os vários [Serviços Cognitivos](welcome.md)
* Saiba mais sobre [redes virtuais de serviços cognitivos](cognitive-services-virtual-networks.md)
