---
title: Prepare VMs Hiper-V para avaliação/migração com Azure Migrate
description: Saiba como se preparar para avaliação/migração de VMs Hiper-V com Migração Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ea6290eae187ed4ead39f7307a84a54a23c19c29
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641640"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para avaliação e migração de VMs Hiper-V para Azure

Este artigo descreve como se preparar para a avaliação de VMs hiper-V no local com [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool), e migração de VMs Hiper-V com [Migração Azure Migrate:Server](migrate-services-overview.md#azure-migrate-server-migration-tool).


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs Hiper-V para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare Azure. Estabeleça permissões para a sua conta Azure e recursos para trabalhar com a Azure Migrate.
> * Prepare no local os anfitriões hiper-V e VMs para avaliação do servidor. Pode preparar-se usando um script de configuração, ou manualmente.
> * Prepare-se para a colocação do aparelho Azure Migrate. O aparelho é utilizado para descobrir e avaliar os VMs no local.
> * Prepare no local os anfitriões hiper-V e VMs para migração do servidor.


> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja os How-tos para avaliação e migração de Hiper-V.


Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões azure

Precisa de permissões para a implantação do Azure Migrate.

**Tarefa** | **Detalhes** 
--- | --- 
**Criar um projeto Azure Migrate** | A sua conta Azure necessita de permissões do Contributer ou do Proprietário para criar um projeto. | 
**Registe os fornecedores de recursos** | A Azure Migrate utiliza um aparelho azure migratório leve para descobrir e avaliar vMs Hiper-V com avaliação do servidor migratório Azure.<br/><br/> Durante o registo do aparelho, os fornecedores de recursos são registados com a assinatura escolhida no aparelho. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.
**Criar aplicação do Azure AD** | Ao registar o aparelho, a Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD) que é utilizada para a comunicação entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Precisa de permissões para criar aplicações Azure AD (disponíveis no Desenvolvedor de Aplicações).



### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

Verifique se tem permissões para criar um projeto Azure Migrate.

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registar o aparelho

Pode atribuir permissões para a Azure Migrate criar a aplicação Azure AD durante o registo do aparelho, utilizando um dos seguintes métodos:

- Um inquilino/administrador global pode conceder permissões aos utilizadores do inquilino, para criar e registar aplicações da Azure AD.
- Um inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações (que tem as permissões) à conta.

> [!NOTE]
> - A aplicação não tem quaisquer outras permissões de acesso na subscrição que não as descritas acima.
> - Só precisa destas permissões quando regista um novo aparelho. Pode retirar as permissões depois de o aparelho ser montado.


#### <a name="grant-account-permissions"></a>Concessão de permissões de conta

O inquilino/administrador global pode conceder permissões da seguinte forma:

1. Em Azure AD, o inquilino/administrador global deve navegar para**as Definições**de**Utilizadores** > de **Diretório** > Ativo Azure .
2. O administrador deve definir **os registos** da App para **Sim**.

    ![Permissões da AD Azure](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Esta é uma definição padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir papel de desenvolvedor de aplicações

O inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Preparar Hyper-V para avaliação

Pode preparar o Hyper-V para a avaliação de VM manualmente, ou utilizando um script de configuração. Os passos de preparação são os seguintes:
- [Verificar](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Configurações de hospedeiro seleção em Hiper-V e certifique-se de que as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos anfitriões Hyper-V.
- Instale o Remo PowerShell em cada hospedeiro, de modo a que o aparelho Azure Migrate possa executar comandos PowerShell no hospedeiro, através de uma ligação WinRM.
- Credenciais de delegado se os discos VM estiverem localizados em ações SMB remotas.
- Criar uma conta que o aparelho utilizará para descobrir VMs em hospedeiros Hyper-V.
- Configurar serviços de integração Hyper-V em cada VM que pretende descobrir e avaliar. As definições predefinidas quando activaos serviços de integração são suficientes para o Azure Migrate.

    ![Ativar serviços de integração](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Prepare-se com um roteiro

O guião faz o seguinte:

- Verifica se estás a executar o guião numa versão PowerShell suportada.
- Verifica que você (o utilizador que executa o script) tem privilégios administrativos no hospedeiro Hyper-V.
- Permite-lhe criar uma conta de utilizador local (não administrador) que o serviço Azure Migrate utiliza para comunicar com o hospedeiro Hyper-V. Esta conta de utilizador é adicionada a estes grupos no anfitrião:
    - Utilizadores de Gestão Remota
    - Administradores de Hyper V
    - Utilizadores do Monitor de Desempenho
- Verifica se o anfitrião está a executar uma versão suportada do Hyper-V, e o papel Hyper-V.
- Ativa o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no anfitrião (necessário para recolha de metadados).
- Permite a remoção powerShell no hospedeiro.
- Verifica se os Serviços de Integração Hyper-V estão ativados em todos os VMs geridos pelo anfitrião.
- Ativa o CredSSP no hospedeiro, se necessário.

Executar o guião da seguinte forma:

1. Certifique-se de que tem a versão 4.0 da PowerShell ou posteriormente instalada no hospedeiro Hyper-V.
2. Descarregue o script do [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). O script é criptograficamente assinado pela Microsoft.
3. Valide a integridade do script utilizando ficheiros de hash MD5 ou SHA256. Os valores de hashtag estão abaixo. Executar este comando para gerar o hash para o script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Utilização de exemplo:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    Depois de validar a integridade do script, execute o script em cada anfitrião Hyper-V com este comando PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Valores hashtag

Os valores de hash são:

| **Estação Hash** | **Valor** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e729925eff4d1ae9f1c7db485dc9316ef45b09648a3c07c80761ade2 |


## <a name="prepare-manually"></a>Preparar manualmente

Siga os procedimentos nesta secção para preparar o Hyper-V manualmente, em vez de utilizar o script.

### <a name="verify-powershell-version"></a>Verifique a versão PowerShell

Certifique-se de que tem a versão 4.0 da PowerShell ou posteriormente instalada no hospedeiro Hyper-V.



### <a name="set-up-an-account-for-vm-discovery"></a>Criar uma conta para a descoberta de VM

A Azure Migrate precisa de permissões para descobrir vMs no local.

- Criar uma conta de domínio ou utilizador local com permissões de administrador nos anfitriões/cluster hyper-V.

    - Precisa de uma única conta para todos os anfitriões e aglomerados que quer incluir na descoberta.
    - A conta pode ser uma conta local ou de domínio. Recomendamos que tenha permissões de administrador nos anfitriões ou clusters hyper-V.
    - Em alternativa, se não quiser atribuir permissões ao Administrador, são necessárias as seguintes permissões:
        - Utilizadores de Gestão Remota
        - Administradores de Hyper V
        - Utilizadores do Monitor de Desempenho

### <a name="verify-hyper-v-host-settings"></a>Verifique as definições do hospedeiro Hyper-V

1. Verifique os requisitos do [hospedeiro Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) para avaliação do servidor.
2. Certifique-se de que as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos anfitriões Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Ativar o remo PowerShell nos anfitriões

Configurar o Remo PowerShell em cada hospedeiro, da seguinte forma:

1. Em cada anfitrião, abra uma consola PowerShell como administrador.
2. Execute este comando:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Ativar serviços de integração em VMs

Os Serviços de Integração devem ser ativados em cada VM para que o Azure Migrate possa capturar informações do sistema operativo no VM.

Em VMs que pretende descobrir e avaliar, permitir [serviços de integração Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM.


### <a name="enable-credssp-on-hosts"></a>Ativar o CredSSP nos anfitriões

Se o anfitrião tiver VMs com discos localizados em ações SMB, complete este passo no anfitrião.

- Pode executar este comando remotamente em todos os anfitriões do Hyper-V.
- Se adicionar novos nós de hospedeiro num cluster, são automaticamente adicionados para descoberta, mas precisa de ativar manualmente o CredSSP nos novos nós, se necessário.

Ativar da seguinte forma:

1. Identifique os anfitriões Hyper-V que executam VMs Hiper-V com discos em ações SMB.
2. Executar o seguinte comando em cada hospedeiro hiper-V identificado:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Quando montar o aparelho, termine de configurar o CredSSP, [ativando-o no aparelho](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Isto é descrito no próximo tutorial desta série.


## <a name="prepare-for-appliance-deployment"></a>Prepare-se para a colocação do aparelho

Antes de configurar o aparelho Azure Migrate e iniciar a avaliação no próximo tutorial, prepare-se para a colocação do aparelho.

1. [Verifique os](migrate-appliance.md#appliance---hyper-v) requisitos do aparelho.
2. Reveja os URLs Azure que o aparelho terá de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls) Se estiver a utilizar uma firewall ou procuração baseada em URL, certifique-se de que permite o acesso aos URLs necessários.
3. Reveja os dados que o aparelho recolherá durante a descoberta e avaliação.
4. [Reveja](migrate-appliance.md#collected-data---hyper-v) os requisitos de acesso à porta do aparelho.




## <a name="prepare-for-hyper-v-migration"></a>Prepare-se para a migração Hyper-V

1. [Revisão](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Requisitos de hospedeiro hiper-V para migração, e os URLs Azure aos quais os anfitriões e aglomerados Hyper-V precisam de acesso para a migração vm.
2. [Reveja](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) os requisitos para VMs hiper-V que pretende migrar para Azure.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar permissões de conta Azure.
> * Anfitriões e V MS preparados para avaliação e migração.
> * Preparado para a colocação do aparelho Azure Migrate.

Continue para o próximo tutorial para criar um projeto Azure Migrate, implementar o aparelho e descobrir e avaliar VMs Hiper-V para migração para Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs hiper-V](./tutorial-assess-hyper-v.md)
