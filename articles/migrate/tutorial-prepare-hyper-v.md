---
title: Preparar VMs Hiper-V para avaliação/migração com Azure Migrate
description: Saiba como se preparar para a avaliação/migração de VMs hiper-V com Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 22fd5bc87494eb2fc162828363e7ca70afe1bbf0
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84322170"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para avaliação e migração de VMs hiper-V para Azure

Este artigo descreve como se preparar para a avaliação de Hiper-V VMs no local com [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool), e migração de VMs Hiper-V com [Azure Migrate:Migração de servidores](migrate-services-overview.md#azure-migrate-server-migration-tool).


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar Hiper-VMs para Azure. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Prepara o Azure. Configure as permissões para a sua conta Azure e recursos para trabalhar com a Azure Migrate.
> * Prepare os anfitriões hiper-V e VMs no local para avaliação do servidor. Pode preparar-se com um script de configuração ou manualmente.
> * Preparar para a colocação do aparelho Azure Migrate. O aparelho é utilizado para descobrir e avaliar os VMs no local.
> * Prepare os anfitriões hiper-V e VMs no local para a migração do servidor.


> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os "How-tos" para avaliação e migração de Hiper-V.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões Azure

Precisa de configurar permissões para a implantação do Azure Migrate.

**Tarefa** | **Detalhes** 
--- | --- 
**Criar um projeto Azure Migrate** | A sua conta Azure precisa de permissões de Contribuidor ou Proprietário para criar um projeto. | 
**Registar fornecedores de recursos** | A Azure Migrate usa um aparelho Azure Migrate leve para descobrir e avaliar os VMs hiper-V com avaliação do servidor Azure Migrate.<br/><br/> Durante o registo do aparelho, os fornecedores de recursos estão registados com a assinatura escolhida no aparelho. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Para registar os fornecedores de recursos, precisa de uma função de Contribuinte ou Proprietário na subscrição.
**Criar aplicação do Azure AD** | Ao registar o aparelho, a Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD) que é utilizada para a comunicação entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Precisa de permissões para criar aplicações AD Azure (disponíveis no Desenvolvedor de Aplicações).



### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

Verifique se tem permissões para criar um projeto Azure Migrate.

1. No portal Azure, abra a subscrição e selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.
3. Deve ter permissões **de Colaborador** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não é o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registar o aparelho

Pode atribuir permissões para a Azure Migrate criar a aplicação AZure AD durante o registo do aparelho, utilizando um dos seguintes métodos:

- Um administrador inquilino/global pode conceder permissões aos utilizadores do arrendatário, para criar e registar aplicações AD Azure.
- Um administrador inquilino/global pode atribuir a função de Desenvolvedor de Aplicações (que tem as permissões) à conta.

> [!NOTE]
> - A aplicação não possui quaisquer outras permissões de acesso na subscrição que não as descritas acima.
> - Só precisa destas permissões quando registar um novo aparelho. Pode retirar as permissões após a instalação do aparelho.


#### <a name="grant-account-permissions"></a>Permissões de conta de subvenção

O arrendatário/administrador global pode conceder permissões da seguinte forma:

1. Em Azure AD, o inquilino/administrador global deve navegar para as Definições de Utilizador de Utilizadores **de Diretório Ativo Azure**  >  **Users**  >  **User Settings**.
2. O administrador deve definir **as inscrições da App** para **Sim.**

    ![Permissões AD AZure](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Esta é uma definição padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicações

O administrador inquilino/global pode atribuir o papel de Desenvolvedor de Aplicações a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Preparar Hiper-V para avaliação

Pode preparar o Hyper-V para avaliação de VM manualmente ou utilizando um script de configuração. Os passos de preparação são os seguintes:
- [Verificar](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) As definições do anfitrião Hiper-V e certifique-se de que as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos anfitriões Hyper-V.
- Configure a remoagem powerShell em cada hospedeiro, de modo a que o aparelho Azure Migrate possa executar comandos PowerShell no hospedeiro, sobre uma ligação WinRM.
- Credenciais de delegado se os discos VM estiverem localizados em ações SMB remotas.
- Crie uma conta que o aparelho utilizará para descobrir VMs em anfitriões Hiper-V.
- Configurar serviços de integração hiper-V em cada VM que pretende descobrir e avaliar. As definições predefinidas quando ativa os Serviços de Integração são suficientes para o Azure Migrate.

    ![Ativar serviços de integração](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Prepare-se com um roteiro

O guião faz o seguinte:

- Verifica se está a executar o script numa versão suportada do PowerShell.
- Verifica se você (o utilizador que executa o script) tem privilégios administrativos no anfitrião Hyper-V.
- Permite-lhe criar uma conta de utilizador local (não administrador) que o serviço Azure Migrate utiliza para comunicar com o anfitrião Hiper-V. Esta conta de utilizador é adicionada a estes grupos no anfitrião:
    - Utilizadores de Gestão Remota
    - Administradores de Hyper V
    - Utilizadores do Monitor de Desempenho
- Verifica se o anfitrião está a executar uma versão suportada do Hyper-V e o papel de Hiper-V.
- Ativa o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no anfitrião (necessário para a recolha de metadados).
- Permite a remoesse powerShell no hospedeiro.
- Verifica se os Serviços de Integração Hiper-V estão ativados em todos os VM geridos pelo anfitrião.
- Ativa o CredSSP no hospedeiro, se necessário.

Execute o guião da seguinte forma:

1. Certifique-se de que tem a versão 4.0 do PowerShell ou posteriormente instalada no anfitrião Hyper-V.
2. Descarregue o script do [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). O guião é assinado criptograficamente pela Microsoft.
3. Valide a integridade do script utilizando ficheiros de haxixe MD5 ou SHA256. Os valores da hashtag estão abaixo. Executar este comando para gerar o haxixe para o script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Utilização de exemplo:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. Depois de validar a integridade do script, execute o script em cada anfitrião Hyper-V com este comando PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Valores de hashtag

Os valores do haxixe são:

| **Haxixe** | **Valor** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Preparar manualmente

Siga os procedimentos desta secção para preparar o Hyper-V manualmente, em vez de utilizar o script.

### <a name="verify-powershell-version"></a>Verificar a versão PowerShell

Certifique-se de que tem a versão 4.0 do PowerShell ou posteriormente instalada no anfitrião Hyper-V.



### <a name="set-up-an-account-for-vm-discovery"></a>Criar uma conta para a descoberta de VM

Azure Migrate precisa de permissões para descobrir VMs no local.

- Crie um domínio ou conta de utilizador local com permissões de administrador nos anfitriões/cluster hiper-V.

    - Você precisa de uma única conta para todos os anfitriões e aglomerados que você quer incluir na descoberta.
    - A conta pode ser uma conta local ou de domínio. Recomendamos que tenha permissões de administrador nos anfitriões ou agrupamentos Hiper-V.
    - Em alternativa, se não quiser atribuir permissões de administrador, são necessárias as seguintes permissões:
        - Utilizadores de Gestão Remota
        - Administradores de Hyper V
        - Utilizadores do Monitor de Desempenho

### <a name="verify-hyper-v-host-settings"></a>Verifique as definições do anfitrião Hyper-V

1. Verifique os [requisitos do anfitrião Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) para avaliação do servidor.
2. Certifique-se de que as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos anfitriões Hiper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Ativar a remoing powerShell nos anfitriões

Configurar a remoing PowerShell em cada hospedeiro, da seguinte forma:

1. Em cada anfitrião, abra uma consola PowerShell como administradora.
2. Execute este comando:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Ativar serviços de integração em VMs

Os Serviços de Integração devem ser ativados em cada VM para que a Azure Migrate possa capturar informações do sistema operativo no VM.

Em VMs que pretende descobrir e avaliar, ative [os Serviços de Integração Hiper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM.


### <a name="enable-credssp-on-hosts"></a>Ativar o CredSSP nos anfitriões

Se o anfitrião tiver VMs com discos localizados em ações SMB, complete este passo no anfitrião.

- Pode executar este comando remotamente em todos os anfitriões Do Hiper-V.
- Se adicionar novos nós de hospedeiro num cluster, são automaticamente adicionados para serem descobertos, mas é necessário ativar manualmente o CredSSP nos novos nós, se necessário.

Ativar o seguinte:

1. Identifique os anfitriões Hiper-V que executam VMs hiper-V com discos em ações SMB.
2. Executar o seguinte comando em cada anfitrião Hiper-V identificado:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Ao configurar o aparelho, termine de configurar o CredSSP, [colocando-o no aparelho](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Isto é descrito no próximo tutorial desta série.


## <a name="prepare-for-appliance-deployment"></a>Preparar para a colocação do aparelho

Antes de configurar o aparelho Azure Migrate e iniciar a avaliação no tutor seguinte, prepare-se para a colocação do aparelho.

1. [Verifique os](migrate-appliance.md#appliance---hyper-v) requisitos do aparelho.
2. Reveja os URLs Azure que o aparelho terá de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls) Se estiver a utilizar uma firewall ou procuração baseada em URL, certifique-se de que permite o acesso aos URLs necessários.
3. Reveja os dados que o aparelho irá recolher durante a descoberta e avaliação.
4. [Reveja](migrate-appliance.md#collected-data---hyper-v) os requisitos de acesso à porta para o aparelho.




## <a name="prepare-for-hyper-v-migration"></a>Preparar para a migração hyper-V

1. [Revisão](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Requisitos de hospedeiro hiper-V para migração, e os URLs Azure aos quais os anfitriões e agrupamentos Hiper-V precisam de acesso para a migração de VM.
2. [Reveja](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) os requisitos para Os Hiper-VMs que pretende migrar para Azure.


## <a name="next-steps"></a>Próximos passos

Neste tutorial:

> [!div class="checklist"]
> * Confise as permissões da conta Azure.
> * Anfitriões e VM preparados para avaliação e migração.
> * Preparado para a colocação do aparelho Azure Migrate.

Continue até ao próximo tutorial para criar um projeto Azure Migrate, implementar o aparelho e descobrir e avaliar VMs Hiper-V para migração para Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs hiper-V](./tutorial-assess-hyper-v.md)
