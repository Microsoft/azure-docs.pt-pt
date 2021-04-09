---
title: Segurança dos Serviços Cognitivos Azure
titleSuffix: Azure Cognitive Services
description: Conheça as várias considerações de segurança para o uso dos Serviços Cognitivos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 90fbc41edbfea0ea99d436906d3052e43bfb4fb8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043883"
---
# <a name="azure-cognitive-services-security"></a>Segurança dos Serviços Cognitivos Azure

A segurança deve ser considerada uma prioridade máxima no desenvolvimento de todas e quaisquer aplicações. Com o início das aplicações ativadas pela inteligência artificial, a segurança é ainda mais importante. Neste artigo são delineados vários aspetos da segurança dos Serviços Cognitivos Azure, tais como a utilização da segurança da camada de transporte, a autenticação, a configuração segura de dados sensíveis e o Bloqueio do Cliente para acesso aos dados dos clientes.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Todos os pontos finais dos Serviços Cognitivos expostos em HTTP aplicam TLS 1.2. Com um protocolo de segurança forçado, os consumidores que tentem chamar um ponto final dos Serviços Cognitivos devem aderir a estas orientações:

* O Sistema Operativo cliente (OS) precisa de suportar tLS 1.2
* O idioma (e plataforma) utilizado para fazer a chamada HTTP precisa de especificar TLS 1.2 como parte do pedido
  * Dependendo do idioma e da plataforma, especificar tLS é feito implicitamente ou explicitamente

Para utilizadores .NET, considere as <a href="/dotnet/framework/network-programming/tls" target="_blank">melhores práticas de Segurança da Camada de Transporte. </a>

## <a name="authentication"></a>Autenticação

Quando se discute a autenticação, existem vários equívocos comuns. A autenticação e a autorização são muitas vezes confundidas umas com as outras. A identidade é também um componente importante na segurança. Uma identidade é uma recolha de informação sobre um <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">principal. </a> Os fornecedores de identidade (IdP) fornecem identidades aos serviços de autenticação. A autenticação é o ato de verificar a identidade de um utilizador. A autorização é a especificação dos direitos de acesso e dos privilégios aos recursos para uma determinada identidade. Várias das ofertas de Serviços Cognitivos incluem o controlo de acesso baseado em funções Azure (Azure RBAC). O Azure RBAC poderia ser usado para simplificar parte da cerimónia envolvida com os diretores de gestão manual. Para mais detalhes, consulte o [controlo de acesso baseado em funções da Azure para os recursos da Azure.](../role-based-access-control/overview.md)

Para obter mais informações sobre a autenticação com chaves de subscrição, tokens de acesso e Diretório Ativo Azure (AAD), consulte <a href="/azure/cognitive-services/authentication" target="_blank">pedidos autenticados aos Serviços Cognitivos da Azure.</a>

## <a name="environment-variables-and-application-configuration"></a>Variáveis ambientais e configuração de aplicações

As variáveis ambientais são pares de valor-nome, armazenados dentro de um ambiente específico. Uma alternativa mais segura à utilização de valores codificados para dados sensíveis é utilizar variáveis ambientais. Os valores codificados são inseguros e devem ser evitados.

> [!CAUTION]
> **Não** utilize valores codificados para dados sensíveis, o que acontece é uma vulnerabilidade de segurança importante.

> [!NOTE]
> Enquanto as variáveis ambientais são armazenadas em texto simples, são isoladas para um ambiente. Se um ambiente está comprometido, também as variáveis com o ambiente.

### <a name="set-environment-variable"></a>Variável do ambiente definido

Para definir variáveis ambientais, use um dos seguintes comandos - onde a `ENVIRONMENT_VARIABLE_KEY` chave é nomeada e é o valor armazenado na `value` variável ambiental.

# <a name="command-line"></a>[Linha de Comando](#tab/command-line)

Criar e atribuir variável ambiente persistiu, dado o valor.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Em um novo caso do **Comando Prompt,** leia a variável ambiental.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Criar e atribuir variável ambiente persistiu, dado o valor.

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

Criar e atribuir variável ambiente persistiu, dado o valor.

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
> Depois de definir uma variável ambiental, reinicie o seu ambiente de desenvolvimento integrado (IDE) para garantir que as novas variáveis ambientais estão disponíveis.

### <a name="get-environment-variable"></a>Obter variável ambiental

Para obter uma variável ambiental, deve ser lido na memória. Dependendo do idioma que está a usar, considere os seguintes cortes de código. Estes fragmentos de código demonstram como obter variável ambiental dada a `ENVIRONMENT_VARIABLE_KEY` e atribuir a uma variável chamada `value` .

# <a name="c"></a>[C#](#tab/csharp)

Para mais informações, consulte. <a href="/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` </a>

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

Para mais informações, consulte. <a href="/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` </a>

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

Para mais informações, consulte. <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` </a>

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

Para mais informações, consulte. <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` </a>

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Para mais informações, consulte. <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` </a>

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Para mais informações, consulte. <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` </a>

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Sistema de Proteção de Dados do Cliente

[O Lockbox do Cliente para o Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. É utilizado nos casos em que um engenheiro da Microsoft precisa de aceder aos dados do cliente durante um pedido de suporte. Para obter informações sobre como os pedidos de Bloqueio do Cliente são iniciados, rastreados e armazenados para posteriores avaliações e auditorias, consulte [o Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md). 

O Lockbox do cliente está disponível para este Serviço Cognitivo:

* Tradutor

Para os seguintes serviços, os engenheiros da Microsoft não acederão a quaisquer dados do cliente no nível E0: 

* Compreensão de Idiomas
* Face
* Content Moderator
* Personalizador

> [!IMPORTANT]
> Para **o Form Recogniser,** os engenheiros da Microsoft não acederão a quaisquer dados do cliente em recursos criados após 10 de julho de 2020.

Para solicitar a capacidade de utilizar o E0 SKU, preencha e envie este [Formulário de Pedido.](https://aka.ms/cogsvc-cmk) Levará aproximadamente 3 a 5 dias úteis para ouvir de volta o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização do E0 SKU com o LUIS, terá de criar um novo recurso a partir do portal Azure e selecionar o E0 como O Nível de Preços. Os utilizadores não poderão fazer upgrade do F0 para o novo E0 SKU.

O serviço de discurso não suporta atualmente o Lockbox do Cliente. No entanto, os dados do cliente podem ser armazenados usando o seu próprio armazenamento (BYOS), permitindo-lhe obter controlos de dados semelhantes ao Lockbox do Cliente. Tenha em mente que os dados do serviço de fala permanecem e são processados na região onde o recurso Discurso foi criado. Isto aplica-se a quaisquer dados em repouso e dados em trânsito. Ao utilizar funcionalidades de personalização, como Discurso Personalizado e Voz Personalizada, todos os dados do cliente são transferidos, armazenados e processados na mesma região onde residem os seus recursos DE SERVIÇO BYOS (se utilizados) e serviço de fala.

> [!IMPORTANT]
> A Microsoft **não** utiliza os dados dos clientes para melhorar os seus modelos De Discurso. Além disso, se a sessão de registo de pontos finais for desativada e não forem utilizadas personalizações, não são armazenados dados do cliente. 

## <a name="next-steps"></a>Passos seguintes

* Explore os vários [Serviços Cognitivos](./what-are-cognitive-services.md)
* Saiba mais sobre [redes virtuais de serviços cognitivos](cognitive-services-virtual-networks.md)