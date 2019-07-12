---
title: Preparar VMs de Hyper-V para avaliação e migração para o Azure com o Azure Migrate | Documentos da Microsoft
description: Descreve como preparar a avaliação e migração de VMs de Hyper-V para o Azure com o Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840376"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para avaliação e migração de VMs de Hyper-V para o Azure

Este artigo descreve como preparar a avaliação e migração de VMs de Hyper-V no local para o Azure com [do Azure Migrate](migrate-services-overview.md).

[O Azure Migrate](migrate-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software. 

Este tutorial é a primeira de uma série que mostra como avaliar e migrar VMs de Hyper-V para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure. Configure permissões para a sua conta do Azure e recursos para trabalhar com o Azure Migrate.
> * Prepare anfitriões de Hyper-V no local e VMs para avaliação de servidor.
> * Prepare VMs e anfitriões de Hyper-V no local para migração do servidor.


> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que pode configurar rapidamente a uma prova de conceito. Tutoriais utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Para obter instruções detalhadas, consulte os guias de avaliação do Hyper-V e a migração.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões do Azure

Precisa de algumas permissões para implementar o Azure Migrate:

- Sua conta do Azure necessita de permissões para criar um projeto do Azure Migrate para avaliação e migração. 
- Sua conta do Azure necessita de permissões para registar a aplicação do Azure Migrate.
    - Para avaliação, o Azure Migrate executa uma aplicação simples que Deteta as VMs de Hyper-V e envia dados de metadados e o desempenho da VM para o Azure Migrate.
    - Durante o registo de aplicação, o Azure Migrate cria duas aplicações do Azure Active Directory (Azure AD) que identificam a aplicação:
        - A primeira aplicação comunica com pontos finais de serviço do Azure Migrate.
        - A segunda aplicação acede a um Azure Key Vault criado durante o registo para armazenar do Azure AD da aplicação e as informações de definições de configuração.
    - Pode atribuir permissões para o Azure Migrate para criar estas aplicações do Azure AD através de um dos seguintes métodos:
        - Um administrador global/inquilino pode conceder permissões aos utilizadores no inquilino, para criar e registar aplicações do Azure AD.
        - Um administrador global/inquilino pode atribuir a função de desenvolvedor do aplicativo (que tem as permissões) para a conta.
    - É importante observar que:
        - As aplicações não tem quaisquer outras permissões de acesso na subscrição que não estão descritas acima.
        - Só tem estas permissões ao registar um novo dispositivo. Pode remover as permissões depois de configurar a aplicação. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

Certifique-se de que tem permissões para criar um projeto do Azure Migrate.

1. No portal do Azure, abra a subscrição e selecione **controlo de acesso (IAM)** .
2. Na **verificar acesso**, localizar a conta relevante e clique nele para ver as permissões.
3. Deve ter **contribuinte** ou **proprietário** permissões.
    - Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registar a aplicação da

Se estiver a implementar a aplicação do Azure Migrate para avaliar as VMs, é preciso registrá-la.

- Durante o registo de aplicação, do Azure Migrate cria duas aplicações do Azure Active Directory (Azure AD) que identificam a aplicação
    - A primeira aplicação comunica com pontos finais de serviço do Azure Migrate.
    - A segunda aplicação acede a um Azure Key Vault criado durante o registo para armazenar do Azure AD da aplicação e as informações de definições de configuração.
- Pode atribuir permissões para o Azure Migrate para criar estas aplicações do Azure AD através de um dos seguintes métodos:
    - Um administrador global/inquilino pode conceder permissões aos utilizadores no inquilino, para criar e registar aplicações do Azure AD.
    - Um administrador global/inquilino pode atribuir a função de desenvolvedor do aplicativo (que tem as permissões) para a conta.

É importante observar que:

- As aplicações não tem quaisquer outras permissões de acesso na subscrição que não estão descritas acima.
- Só tem estas permissões ao registar um novo dispositivo. Pode remover as permissões depois de configurar a aplicação. 


#### <a name="grant-account-permissions"></a>Conceder permissões de conta

O administrador de inquilino/global pode conceder permissões da seguinte forma:

1. No Azure AD, o administrador global/inquilino deve acessar **do Azure Active Directory** > **utilizadores** > **as definições de utilizador**.
2. O administrador deve definir **registos de aplicações** ao **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Esta é uma definição de predefinição que não seja confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir a função de programador da aplicação 

O administrador global/inquilino pode atribuir a função de programador da aplicação a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Preparar para a avaliação do Hyper-V

Para preparar a avaliação do Hyper-V, verifique se o anfitrião Hyper-V e as definições da VM e verifique as definições para a implementação da aplicação.

### <a name="verify-hyper-v-host-settings"></a>Verifique se as definições de anfitrião do Hyper-V

1. Certifique-se [requisitos de anfitrião do Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) da avaliação server.
2. Certifique-se de que o [necessárias portas](migrate-support-matrix-hyper-v.md#assessment-port-requirements) estão abertas nos anfitriões Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Ativar a comunicação remota do PowerShell em anfitriões

Configure a comunicação remota do PowerShell em cada anfitrião, da seguinte forma:

1. Em cada anfitrião, abra uma consola do PowerShell como administrador.
2. Execute este comando:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Ativar o CredSSP nos anfitriões

Se os discos VM estiverem localizados em partilhas SMB, conclua este passo em cada anfitrião de Hyper-V relevante. Este passo é utilizado para detetar informações de configuração para VMs de Hyper-V com discos em partilhas SMB. Se não tiver discos da VM em partilhas SMB, pode ignorar o passo.

1. Identifica anfitriões de Hyper-V com VMs Hyper-V com discos em partilhas SMB.
2. Execute o seguinte comando em cada anfitrião de Hyper-V identificado:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- Autenticação CredSSP permite que o anfitrião de Hyper-V delegar credenciais em nome do cliente do Azure Migrate.
- Pode executar este comando remotamente em todos os anfitriões de Hyper-V.
- Se adicionar novos nós de anfitrião num cluster é automaticamente adicionado para a deteção, mas tem de ativar manualmente CredSSP em nós de novo, se necessário.

### <a name="verify-appliance-settings"></a>Verifique as definições de aplicação

Antes de configurar o Azure Migrate da aplicação e a avaliação de início no tutorial seguinte, prepare-se para a implementação da aplicação.

1. [Certifique-se de](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) requisitos da aplicação.
2. [Revisão](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) os URLs do Azure que a aplicação precisa de acesso.
3. Reveja os dados que a aplicação irá recolher durante a deteção e avaliação.
4. [Tenha em atenção](migrate-support-matrix-hyper-v.md#assessment-port-requirements) porta requisitos de acesso da aplicação.


### <a name="set-up-an-account-for-vm-discovery"></a>Configurar uma conta para deteção de VMS

O Azure Migrate necessita de permissões para detetar VMs no local.

- Configure um domínio ou conta de utilizador local com permissões de administrador no anfitriões/cluster de Hyper-V.

    - Precisa de uma única conta para todos os anfitriões e clusters que pretende incluir na deteção.
    - A conta pode ser uma conta local ou de domínio. Recomendamos que tem permissões de administrador nos anfitriões Hyper-V ou clusters.
    - Em alternativa, se não quiser atribuir permissões de administrador, são necessárias as seguintes permissões:
        - Utilizadores de gestão remota
        - Administradores de Hyper-V
        - Usuários de Monitor de desempenho

### <a name="enable-integration-services-on-vms"></a>Ativar os serviços de integração em VMs

Serviços de integração devem ser ativados em cada VM para que o Azure Migrate pode capturar informações do sistema operativo na VM.

- Em VMs que pretende detetar e avaliar, ative [serviços de integração do Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM. 

## <a name="prepare-for-hyper-v-migration"></a>Preparar a migração do Hyper-V

1. [Revisão](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) requisitos de anfitrião de Hyper-V para migração.
2. [Revisão](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) os requisitos para VMs de Hyper-V que pretende migrar para o Azure.
3. [Tenha em atenção](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) os URLs do Azure para o Hyper-V e clusters de anfitriões precisam de acesso para a migração de VM.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial:
 
> [!div class="checklist"] 
> * Configure permissões de conta do Azure.
> * Anfitriões Hyper-V e VMs para avaliação e migração preparado.

Continuar para o próximo tutorial para criar um projeto do Azure Migrate e avaliar VMs de Hyper-V para migração para o Azure

> [!div class="nextstepaction"] 
> [Avaliar as VMs de Hyper-V](./tutorial-assess-hyper-v.md) 
