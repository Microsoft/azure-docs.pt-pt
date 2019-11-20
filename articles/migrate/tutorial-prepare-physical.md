---
title: Preparar servidores físicos para avaliação/migração com as migrações para Azure
description: Saiba como se preparar para a avaliação/migração de servidores físicos com as migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 363549662a17a87513c8426347909142ee405cae
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196387"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Preparar para avaliação e migração de servidores físicos para o Azure

Este artigo descreve como se preparar para a avaliação de servidores físicos locais com as [migrações para Azure](migrate-services-overview.md).

As [migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros. 

Este tutorial é o primeiro de uma série que mostra como avaliar servidores físicos com as migrações para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure. Configure permissões para sua conta do Azure e recursos para trabalhar com as migrações para Azure.
> * Preparar servidores físicos locais para avaliação do servidor.


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, examine a avaliação de como o-tos para servidores físicos.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões do Azure

Você precisa configurar permissões para a implantação de migrações para Azure.

- Permissões para sua conta do Azure para criar um projeto de migrações para Azure.
- Permissões para sua conta para registrar o dispositivo de migrações para Azure. O dispositivo é usado para descoberta e migração do Hyper-V. Durante o registro do dispositivo, as migrações para Azure criam dois aplicativos Azure Active Directory (Azure AD) que identificam exclusivamente o dispositivo:
    - O primeiro aplicativo se comunica com os pontos de extremidade de serviço de migrações para Azure.
    - O segundo aplicativo acessa uma Azure Key Vault que é criada durante o registro, para armazenar as informações de aplicativo do Azure AD e as configurações de dispositivo.



### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

Verifique se você tem permissões para criar um projeto de migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões.
3. Você deve ter permissões de **colaborador** ou de **proprietário** .
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Você pode atribuir permissões para migrações para Azure para criar os aplicativos do Azure AD criados durante o registro do dispositivo, usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo (que tem as permissões) à conta.

Vale a pena observar que:

- Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
- Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo for configurado.


#### <a name="grant-account-permissions"></a>Permissões de conta de concessão

O locatário/administrador global pode conceder permissões da seguinte maneira:

1. No Azure AD, o locatário/administrador global deve navegar até **Azure Active Directory** > **usuários** > **configurações de usuário**.
2. O administrador deve definir **registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Preparar a avaliação do servidor físico

Para se preparar para a avaliação do servidor físico, você precisa verificar as configurações do servidor físico e verificar as configurações da implantação do dispositivo:

### <a name="verify-physical-server-settings"></a>Verificar as configurações do servidor físico

1. Verifique [os requisitos do servidor físico](migrate-support-matrix-physical.md#assessment-physical-server-requirements) para avaliação do servidor.
2. Verifique se as [portas necessárias](migrate-support-matrix-physical.md#assessment-port-requirements) estão abertas em servidores físicos.


### <a name="verify-appliance-settings"></a>Verificar as configurações do dispositivo

Antes de configurar o dispositivo migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-support-matrix-physical.md#assessment-appliance-requirements) os requisitos do dispositivo.
2. [Examine](migrate-support-matrix-physical.md#assessment-appliance-url-access) as URLs do Azure que o dispositivo precisará acessar.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Anote](migrate-support-matrix-physical.md#assessment-port-requirements) os requisitos de acesso da porta para o dispositivo.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurar uma conta para descoberta de servidor físico

As migrações para Azure precisam de permissões para descobrir servidores locais.

- **Windows:** Configure uma conta de usuário local em todos os servidores Windows que você deseja incluir na descoberta. A conta de usuário precisa ser adicionada aos seguintes grupos:-usuários de gerenciamento remoto-usuários do monitor de desempenho-usuários de log de desempenho
- **Linux:** Você precisa de uma conta raiz nos servidores Linux que deseja descobrir.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar permissões de conta do Azure.
> * Servidores físicos preparados para avaliação.

Continue no próximo tutorial para criar um projeto de migrações para Azure e avaliar servidores físicos para migração para o Azure

> [!div class="nextstepaction"]
> [Avaliar servidores físicos](./tutorial-assess-physical.md)
