---
title: 'Pedido de Gémeos Digitais Azure falhou com Status: 403 (Proibido)'
description: "As causas e as resoluções para o pedido de serviço falharam. Estado: 403 (Proibido)' em Azure Digital Twins."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: bc4fbbc265bef00be27c890c3f090a49591dc415
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90562745"
---
# <a name="service-request-failed-status-403-forbidden"></a>O pedido de serviço falhou. Estado: 403 (Proibido)

Este artigo descreve as causas e as etapas de resolução para receber um erro 403 dos pedidos de serviço à Azure Digital Twins. 

## <a name="symptoms"></a>Sintomas

Este erro pode ocorrer em muitos tipos de pedidos de serviço que requerem autenticação. O efeito é que o pedido da API falha, devolvendo um estado de erro de `403 (Forbidden)` .

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa #1

Na maioria das vezes, este erro indica que as permissões de controlo de acesso (RBAC) baseadas em funções para o serviço não estão corretamente configuradas. Muitas ações para uma instância Azure Digital Twins requerem que você tenha o papel de *Azure Digital Twins Owner (Preview)* **sobre o caso que você está tentando gerir**. 

### <a name="cause-2"></a>Causa #2

Se estiver a utilizar uma aplicação para clientes para comunicar com a Azure Digital Twins, este erro pode acontecer porque o seu registo de aplicações [Azure Ative (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) não tem permissões configuradas para o serviço Azure Digital Twins.

O registo da aplicação é necessário para ter permissões de acesso configuradas para as APIs das Gémeas Digitais Azure. Em seguida, quando a aplicação do seu cliente autenticar contra o registo da aplicação, serão concedidas as permissões que o registo da aplicação configura.

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>#1 de solução

A primeira solução é verificar se o utilizador Azure tem o papel _**de Azure Digital Twins Owner (Preview)**_ no caso em que está a tentar gerir. Se não tem este papel, crie-o.

Note que este papel é diferente de...
* a função *do Proprietário* em toda a subscrição do Azure. *Azure Digital Twins Owner (Preview)* é uma função dentro da Azure Digital Twins e é traçada para este exemplo individual de Azure Digital Twins.
* o papel *de Proprietário* em Azure Digital Twins. Estes são dois papéis distintos de gestão da Azure Digital Twins, e *a Azure Digital Twins Owner (Preview)* é o papel que deve ser usado para a gestão durante a pré-visualização.

#### <a name="check-current-setup"></a>Verifique a configuração atual

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Corrigir problemas 

Se não tiver esta atribuição de funções, alguém com um papel proprietário na sua **subscrição Azure** deve executar o seguinte comando para dar ao seu utilizador Azure a função *Azure Digital Twins Owner (Preview)* na **instância Azure Digital Twins**. 

Se você é um Proprietário na subscrição, você pode executar este comando por si mesmo. Se não estiver, contacte um Proprietário para executar este comando em seu nome.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Para obter mais detalhes sobre este requisito de função e o processo de atribuição, consulte a secção de [ *permissões* ](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) de acesso do seu utilizador de *Como-a-: Configurar uma instância e autenticação (CLI ou portal)*.

Se já tem esta função e ainda encontra a edição 403, continue para a próxima solução.

### <a name="solution-2"></a>#2 de solução

A segunda solução é verificar se o registo da aplicação AZure AD tem permissões configuradas para o serviço Azure Digital Twins. Se isto não estiver configurado, instale-os.

#### <a name="check-current-setup"></a>Verifique a configuração atual

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Em primeiro lugar, verifique se as permissões das permissões Azure Digital Twins foram corretamente definidas no registo. Para isso, selecione *Manifesto* da barra de menu para ver o código manifesto do registo da aplicação. Percorra a parte inferior da janela de código e procure estes campos sob `requiredResourceAccess` . Os valores devem corresponder aos da imagem abaixo:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>Corrigir problemas

Se alguma destas coisas aparecer de forma diferente da descrita, siga as instruções sobre como configurar um registo de aplicações na secção [ *De acesso a aplicações do cliente* ](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) de *Como-a-: Configurar uma instância e autenticação (CLI ou portal)*.

## <a name="next-steps"></a>Passos seguintes

Leia os passos de configuração para criar e autenticar um novo exemplo de Azure Digital Twins:
* [*Como: Configurar um caso e autenticação (CLI)*](how-to-set-up-instance-cli.md)

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)