---
title: Erro de autenticação do Azure Digital Twins Explorer
description: Causas e resoluções para "Autenticação falhou". no Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495086"
---
# <a name="authentication-failed"></a>A autenticação falhou

Este artigo descreve as causas e os passos de resolução para receber um erro de "Autenticação falhada" enquanto executa a amostra [do Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) na sua máquina local. 

## <a name="symptoms"></a>Sintomas

Ao configurar e executar a aplicação Azure Digital Twins Explorer, as tentativas de autenticação com a aplicação são recebidas com a seguinte mensagem de erro:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Screenshot de uma mensagem de erro no explorador Azure Digital Twins com o seguinte texto:A autenticação falhou. Se estiver a executar a aplicação localmente, certifique-se de que está a iniciar sessão no Azure na sua máquina de anfitrião, ou exemplo, executando 'az login' num pedido de comando, iniciando a sessão no Visual Studio ou VS Code ou definindo variáveis ambientais. Se precisar de mais informações, consulte a leitura ou procure o DefaultAzureCredential na documentação Azure.Identity. Se estiver a executar um adt-explorer hospedado na nuvem, certifique-se de que o seu anfitrião Azure Function tem uma identidade gerida atribuída ao sistema. Consulte a leitura para mais informações.":::

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa #1

A aplicação Azure Digital Twins Explorer utiliza [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (parte da `Azure.Identity` biblioteca), que procurará credenciais dentro do seu ambiente local.

Como o texto de erro indica, este erro pode ocorrer se não tiver fornecido credenciais locais `DefaultAzureCredential` para recolher.

Para obter mais informações sobre a utilização de credenciais locais com o Azure Digital Twins Explorer, consulte a secção de [*credenciais Azure local*](quickstart-adt-explorer.md#set-up-local-azure-credentials) do Quickstart Azure Digital *Twins: Explore um cenário de amostragem*.

### <a name="cause-2"></a>Causa #2

Este erro também pode ocorrer se a sua conta Azure não tiver as permissões necessárias de controlo de acesso baseado em funções (Azure RBAC) definidas na sua instância Azure Digital Twins. Para aceder aos dados no seu caso, tem de ter o **leitor de dados Azure Digital Twins** ou o **Azure Digital Twins Data Owner** no caso em que está a tentar ler ou gerir, respectivamente. 

Para obter mais informações sobre segurança e papéis em Azure Digital Twins, consulte [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md).

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>#1 de solução

Primeiro, certifique-se de que forneceu as credenciais necessárias para a aplicação.

#### <a name="provide-local-credentials"></a>Fornecer credenciais locais

`DefaultAzureCredential` autentica-se ao serviço utilizando a informação de um azure local. Pode fornecer as suas credenciais Azure insinuá-lo na sua conta Azure numa janela [Azure CLI](/cli/azure/install-azure-cli) local, ou no Visual Studio ou Visual Studio Code.

Pode ver os tipos de credenciais que `DefaultAzureCredential` aceitam, bem como a ordem pela qual são tentados, na documentação de [identidade Azure para DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

Se já assinou localmente na conta Azure certa e o problema não está resolvido, continue para a próxima solução.

### <a name="solution-2"></a>#2 de solução

Verifique se o utilizador do Azure tem o papel **de Leitor de Dados de Gémeos Digitais Azure** na instância Azure Digital Twins se estiver apenas a tentar ler os seus dados, ou a função **de Proprietário de Dados de Gémeos Digitais Azure** no caso se estiver a tentar gerir os seus dados.

Note que este papel é diferente de...
* o nome anterior para este papel durante a pré-visualização, *Azure Digital Twins Owner (Preview)* (o papel é o mesmo, mas o nome mudou)
* a função *do Proprietário* em toda a subscrição do Azure. *Azure Digital Twins Data Owner* é um papel dentro da Azure Digital Twins e é traçado para este exemplo individual de Azure Digital Twins.
* o papel *de Proprietário* em Azure Digital Twins. Estes são dois papéis distintos de gestão da Azure Digital Twins, e *a Azure Digital Twins Data Owner* é o papel que deve ser usado para a gestão.

 Se não tiver este papel, crie-o para resolver o problema.

#### <a name="check-current-setup"></a>Verifique a configuração atual

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Corrigir problemas 

Se não tiver esta atribuição de funções, alguém com um papel proprietário na sua **subscrição Azure** deve executar o seguinte comando para dar ao seu utilizador Azure o papel adequado na **instância Azure Digital Twins**. 

Se você é um Proprietário na subscrição, você pode executar este comando por si mesmo. Se não estiver, contacte um proprietário para executar este comando em seu nome. O nome da função é **Azure Digital Twins Data Owner** para acesso de edição ou **Azure Digital Twins Data Reader** para acesso de leitura.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Para obter mais detalhes sobre este requisito de função e o processo de atribuição, consulte a secção de [ *permissões*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) de acesso do seu utilizador de *Como-a-: Configurar uma instância e autenticação (CLI ou portal)*.

## <a name="next-steps"></a>Passos seguintes

Leia os passos de configuração para criar e autenticar um novo exemplo de Azure Digital Twins:
* [*Como: Configurar um caso e autenticação (CLI)*](how-to-set-up-instance-cli.md)

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)
