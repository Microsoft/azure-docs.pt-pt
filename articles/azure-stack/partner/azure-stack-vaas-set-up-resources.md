---
title: Tutorial - configurar recursos para a validação como um serviço | Documentos da Microsoft
description: Neste tutorial, saiba como configurar recursos para a validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 55c9120547472bb9a9a74533fe532d346844e89c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081768"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Tutorial: Configurar recursos para a validação como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A validação como um serviço (VaaS) é um serviço do Azure que serve para validar e suportar soluções do Azure Stack no mercado. Siga este artigo antes de utilizar o serviço para validar a sua solução.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare-se de que utilize VaaS através da definição de cópia de segurança do Azure Active Directory (AD).
> * Criar uma conta de armazenamento.

## <a name="configure-an-azure-ad-tenant"></a>Configurar um inquilino do Azure AD

Um inquilino do Azure AD é utilizado para registar uma organização e autenticar utilizadores com VaaS. O parceiro irá utilizar as funcionalidades de controlo (RBAC) de acesso baseado em função do inquilino para gerir quem na organização do parceiro pode utilizar VaaS. Para obter mais informações, consulte [What is Azure Active Directory? (O que é o Azure Active Directory?)](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Criar um inquilino

Crie um inquilino que sua organização irá utilizar para aceder aos serviços de VaaS. Utilizar um nome descritivo, por exemplo, `ContosoVaaS@onmicrosoft.com`.

1. Criar um inquilino do Azure AD no [portal do Azure](https://portal.azure.com), ou utilize um inquilino existente. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Adicione membros da sua organização para o inquilino. Estes utilizadores será responsáveis pela utilização do serviço para ver ou agendar os testes. Depois de concluir o registo, irá definir níveis de acesso dos utilizadores.

    Autorize os utilizadores no seu inquilino para executar ações no VaaS atribuindo uma das seguintes funções:

    | Nome da Função | Descrição |
    |---------------------|------------------------------------------|
    | Proprietário | Tem acesso total a todos os recursos. |
    | Leitor | Pode ver todos os recursos, mas não criar ou gerir. |
    | Contribuinte de teste | Pode criar e gerir recursos de teste. |

    Para atribuir funções no **serviço de validação do Azure Stack** aplicação:

   1. Inicie sessão no [portal do Azure](https://portal.azure.com).
   2. Selecione **todos os serviços** > **do Azure Active Directory** sob o **identidade** secção.
   3. Selecione **aplicações empresariais** > **serviço de validação do Azure Stack** aplicação.
   4. Selecionar **Utilizadores e grupos**. O **serviço do Azure Stack validação - os utilizadores e grupo** painel mostra os utilizadores com permissão para utilizar a aplicação.
   5. Selecione **+ adicionar utilizador** para adicionar um utilizador do seu inquilino e atribuir uma função.

      Se quiser isolar VaaS recursos e ações em diferentes grupos dentro de uma organização, pode criar vários diretórios de inquilino do Azure AD.

### <a name="register-your-tenant"></a>Registar o seu inquilino

Este processo autoriza o seu inquilino com o **serviço de validação do Azure Stack** aplicação do Azure AD.

1. Enviar as seguintes informações sobre o inquilino para a Microsoft em [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

    | Dados | Descrição |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nome da Organização | O nome da organização oficial. |
    | Nome de diretório do inquilino do Azure AD | O nome do diretório de inquilino do Azure AD que está a ser registado. |
    | ID de diretório do inquilino do Azure AD | O diretório de inquilino do Azure AD, GUID associada ao diretório. Para obter informações sobre como encontrar o ID do inquilino do Azure AD Directory, consulte [obter ID de inquilino](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Aguarde pela confirmação da equipa do Azure Stack validação para verificar que o seu inquilino pode utilizar o portal de VaaS.

### <a name="consent-to-the-vaas-application"></a>Autorizar a aplicação de VaaS

Como administrador do Azure AD, dar, por exemplo, as permissões necessárias em nome do seu inquilino ao aplicativo VaaS do Azure AD:

1. Utiliza as credenciais de administrador global do inquilino para iniciar sessão para o [VaaS portal](https://azurestackvalidation.com/). 

2. Selecione **minha conta**.

3 aceitar os termos para continuar quando lhe for pedido para conceder permissões de VaaS o Azure AD listado.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Durante a execução de teste, VaaS produz os registos de diagnóstico para uma conta de armazenamento do Azure. Além dos registos de teste, a conta de armazenamento pode também ser utilizadas para os pacotes de extensão de carregamento do OEM para o fluxo de trabalho de validação do pacote.

A conta de armazenamento do Azure está alojada na cloud pública do Azure, não no seu ambiente do Azure Stack.

1. No portal do Azure, selecione **todos os serviços** > **armazenamento** > **contas de armazenamento**. Sobre o **contas de armazenamento** painel, selecione **Add**.

2. Selecione a subscrição na qua pretende criar a conta de armazenamento.

3. Sob **grupo de recursos**, selecione **criar nova**. Introduza um nome para o novo grupo de recursos.

4. Reveja os [convenções de nomenclatura](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions#storage) para contas de armazenamento do Azure. Introduza um nome para a conta do Storage.

5. Selecione o **E.u.a. oeste** região para a sua conta de armazenamento.

    Para garantir que não são cobrados custos de funcionamento em rede para armazenar registos, a conta de armazenamento do Azure pode ser configurada para utilizar apenas o **E.u.a. oeste** região. Replicação de dados e a funcionalidade de camada de armazenamento frequente não são necessários para estes dados. Ativar a funcionalidade irá aumentar, significativamente os custos.

6. Deixe as definições para os valores predefinidos exceto para **tipo de conta**:

    - O **modelo de implementação** campo é definido como **Resource Manager** por predefinição.
    - Por predefinição, o campo **Desempenho** é definido como **Padrão**.
    - Selecione **tipo de conta** campo como **armazenamento de BLOBs**.
    - O **campo de replicação** está definida como **armazenamento localmente redundante (LRS)** por predefinição.
    - Por predefinição, **Camada de acesso** está definida como **Frequente**.

7. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.

## <a name="next-steps"></a>Passos Seguintes

Se o seu ambiente não permite ligações-bound, siga o tutorial sobre como implementar o agente local para executar um teste no seu hardware.

> [!div class="nextstepaction"]
> [Implementar o agente local](azure-stack-vaas-local-agent.md)
