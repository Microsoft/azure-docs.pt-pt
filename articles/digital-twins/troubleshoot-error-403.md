---
title: 'Pedido de Gémeos Digitais Azure falhou com Status: 403 (Proibido)'
description: "As causas e as resoluções para o pedido de serviço falharam. Estado: 403 (Proibido)' em Azure Digital Twins."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: ad28b3300e9107c7f6d4b1987205583ef60ed658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545705"
---
# <a name="service-request-failed-status-403-forbidden"></a>O pedido de serviço falhou. Estado: 403 (Proibido)

Este artigo descreve as causas e as etapas de resolução para receber um erro 403 dos pedidos de serviço à Azure Digital Twins. 

## <a name="symptoms"></a>Sintomas

Este erro pode ocorrer em muitos tipos de pedidos de serviço que requerem autenticação. O efeito é que o pedido da API falha, devolvendo um estado de erro de `403 (Forbidden)` .

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa #1

Na maioria das vezes, este erro indica que as permissões de controlo de acesso baseado em funções (Azure RBAC) para o serviço não estão corretamente configuradas. Muitas ações para uma instância Azure Digital Twins requerem que você tenha o papel *de Proprietário de Dados Azure Digital Twins* sobre o caso que você está **tentando gerir.** 

### <a name="cause-2"></a>Causa #2

Se estiver a utilizar uma aplicação de cliente para comunicar com a Azure Digital Twins que está a autenticar com um [registo de aplicações](how-to-create-app-registration.md), este erro pode acontecer porque o registo da sua aplicação não tem permissões configurada para o serviço Azure Digital Twins.

O registo da aplicação deve ter permissões de acesso configuradas para as APIs das Gémeas Digitais Azure. Em seguida, quando a aplicação do seu cliente autenticar contra o registo da aplicação, serão concedidas as permissões que o registo da aplicação configura.

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>#1 de solução

A primeira solução é verificar se o utilizador do Azure tem a função _**de Proprietário de Dados Azure Digital Twins**_ no caso em que está a tentar gerir. Se não tem este papel, crie-o.

Note que este papel é diferente de...
* o nome anterior para este papel durante a pré-visualização, *Azure Digital Twins Owner (Preview)* (o papel é o mesmo, mas o nome mudou)
* a função *do Proprietário* em toda a subscrição do Azure. *Azure Digital Twins Data Owner* é um papel dentro da Azure Digital Twins e é traçado para este exemplo individual de Azure Digital Twins.
* o papel *de Proprietário* em Azure Digital Twins. Estes são dois papéis distintos de gestão da Azure Digital Twins, e *a Azure Digital Twins Data Owner* é o papel que deve ser usado para a gestão.

#### <a name="check-current-setup"></a>Verifique a configuração atual

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Corrigir problemas 

Se não tiver esta atribuição de funções, alguém com um papel proprietário na sua **subscrição Azure** deve executar o seguinte comando para dar ao seu utilizador Azure a função *de Proprietário de Dados Azure Digital Twins* na instância **Azure Digital Twins**. 

Se você é um Proprietário na subscrição, você pode executar este comando por si mesmo. Se não estiver, contacte um proprietário para executar este comando em seu nome.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

Para obter mais detalhes sobre este requisito de função e o processo de atribuição, consulte a secção de [ *permissões*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) de acesso do seu utilizador de *Como-a-: Configurar uma instância e autenticação (CLI ou portal)*.

Se já tem esta atribuição de funções *e* está a utilizar um registo de aplicações AD AZure para autenticar uma aplicação de clientes, pode continuar a solução seguinte se esta solução não resolver a questão 403.

### <a name="solution-2"></a>#2 de solução

Se estiver a utilizar um registo de aplicações AD AZure para autenticar uma aplicação para clientes, a segunda solução possível é verificar se o registo da aplicação tem permissões configuradas para o serviço Azure Digital Twins. Se estes não estiverem configurados, instale-os.

#### <a name="check-current-setup"></a>Verifique a configuração atual

Para verificar se as permissões foram configuradas corretamente, navegue para a página geral de registo da [aplicação AD Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) no portal Azure. Você mesmo pode chegar a esta página procurando *registos de Aplicações* na barra de pesquisa do portal.

Mude para o separador *Todas as aplicações* para ver todos os registos de aplicações que foram criados na sua subscrição.

Você deve ver o registo de aplicações que acabou de criar na lista. Selecione-o para abrir os seus detalhes.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Página de registos de aplicativos no portal Azure":::

Em primeiro lugar, verifique se as permissões das permissões Azure Digital Twins foram corretamente definidas no registo. Para isso, selecione *Manifesto* da barra de menu para ver o código manifesto do registo da aplicação. Percorra a parte inferior da janela de código e procure estes campos sob `requiredResourceAccess` . Os valores devem corresponder aos da imagem abaixo:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Vista portal do manifesto para o registo da app AZure AD":::

Em seguida, selecione *permissões API* da barra de menu para verificar se este registo de aplicações contém permissões de Read/Write para Azure Digital Twins. Devia ver uma entrada como esta:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Vista do portal das permissões da API para o registo da aplicação AZure AD, mostrando 'Read/Write Access' para Azure Digital Twins":::

#### <a name="fix-issues"></a>Corrigir problemas

Se alguma destas coisas aparecer de forma diferente da descrita, siga as instruções sobre como configurar um registo de aplicações em [*How-to: Criar um registo de aplicações*](how-to-create-app-registration.md).

## <a name="next-steps"></a>Passos seguintes

Leia os passos de configuração para criar e autenticar um novo exemplo de Azure Digital Twins:
* [*Como: Configurar um caso e autenticação (CLI)*](how-to-set-up-instance-cli.md)

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)
