---
title: Preparar VMs Hiper-V para avaliação/migração com Azure Migrate
description: Saiba como se preparar para a avaliação/migração de VMs hiper-V com Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109625"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para avaliação e migração de VMs hiper-V para Azure

Este artigo ajuda-o a preparar-se para a avaliação e migração de VMs Hiper-V para Azure, utilizando [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool), e [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar Hiper-VMs para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure para trabalhar com a Azure Migrate.
> * Preparem-se para avaliar os Hiper-VMs.
> * Preparar para migrar Hiper-VMs 

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

A tabela resume as tarefas que precisa de completar em Azure. As instruções seguem a mesa.

**Tarefa** | **Detalhes** | **Permissões**
--- | --- | ---
**Criar um projeto Azure Migrate** | Um projeto Azure Migrate fornece uma localização central para orquestrar e gerir avaliações e migrações com ferramentas Azure Migrate, ferramentas Microsoft e ofertas de terceiros. | A sua conta Azure necessita de permissões de Contribuinte ou Proprietário no grupo de recursos em que o projeto reside.
**Aparelho de registo** | A Azure Migrate utiliza um aparelho Azure Migrate leve para descobrir e avaliar os Hiper-VM. [Saiba mais](migrate-appliance-architecture.md#appliance-registration). | Para registar o aparelho, a sua conta Azure necessita de permissões de Contribuinte ou Proprietário na assinatura Azure.
**Criar aplicação do Azure AD** | Ao registar o aparelho, a Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD) que é utilizada para a comunicação entre os agentes que estão a trabalhar no aparelho e a Azure Migrate. | A sua conta Azure necessita de permissões para criar aplicações AD Azure.
**Criar uma VM** | Precisa de permissões para criar um VM no grupo de recursos e rede virtual, e para escrever para um disco gerido pelo Azure. | A conta Azure precisa do papel de Contribuinte de Máquina Virtual.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

Verifique se tem permissões para criar um projeto Azure Migrate.

1. No portal Azure, abra a subscrição e selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.
3. Deve ter permissões **de Colaborador** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não é o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Atribuir permissões para criar aplicações AD Azure

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
> Esta é uma definição padrão que não é sensível. [Saiba mais](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicações

O administrador inquilino/global pode atribuir o papel de Desenvolvedor de Aplicações a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta Azure

Atribua a função contribuinte da máquina virtual à conta, para que tenha permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Escreva para um disco gerido pelo Azure. 


### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

[Crie uma rede Azure.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) As máquinas no local são replicadas em discos geridos aZure. Quando falhas no Azure para migração, os VMs Azure são criados a partir destes discos geridos, e unidos à rede Azure que criaste.


## <a name="prepare-for-assessment"></a>Preparar para avaliação

Pode preparar o Hyper-V para avaliação de VM manualmente ou utilizando um script de configuração. Estes são os passos de preparação. Se se preparar com um script, estes são configurados automaticamente.

**Passo** | **Roteiro** | **Manual**
--- | --- | ---
**Verifique os requisitos do hospedeiro Hyper-V** | O script verifica se o anfitrião está a executar uma versão suportada do Hyper-V e o papel de Hiper-V.<br/><br/> Ativa o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no anfitrião (necessário para a recolha de metadados). | Verifique os [requisitos do anfitrião Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) para avaliação do servidor.<br/><br/> Certifique-se de que as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos anfitriões Hiper-V.
**Verificar a versão PowerShell** | Verifica se está a executar o script numa versão suportada do PowerShell. | Verifique se está a executar a versão 4.0 do PowerShell ou mais tarde no anfitrião Hyper-V.
**Criar uma conta** | Verifica se você (o utilizador que executa o script) tem privilégios administrativos no anfitrião Hyper-V.<br/><br/>  Permite-lhe criar uma conta de utilizador local (não administrador) que o serviço Azure Migrate utiliza para comunicar com o anfitrião Hiper-V. Esta conta de utilizador é adicionada a estes grupos no anfitrião:<br/><br/> - Utilizadores de Gestão Remota<br/><br/> - Administradores hiper-V<br/><br/>- Utilizadores do Monitor de Desempenho | Crie um domínio ou conta de utilizador local com permissões de administrador nos anfitriões/cluster hiper-V.<br/><br/> - Você precisa de uma única conta para todos os anfitriões e aglomerados que você quer incluir na descoberta.<br/><br/> - A conta pode ser uma conta local ou de domínio. Recomendamos que tenha permissões de administrador nos anfitriões ou agrupamentos Hiper-V.<br/><br/> Em alternativa, se não quiser atribuir permissões de Administrador, são necessárias as seguintes permissões: Utilizadores de Gestão Remota; Administradores hiper-V; Utilizadores do Monitor de Desempenho.
**Ativar a remoing powerShell** | Ativa a remoagem powerShell no hospedeiro, de modo a que o aparelho Azure Migrate possa executar comandos PowerShell no hospedeiro, sobre uma ligação WinRM.| Para configurar, em cada anfitrião, abra uma consola PowerShell como administrador, e executar este comando:<br/><br/>``` Enable-PSRemoting -force ```
**Criar serviços de integração hiper-V** | Verifica se os Serviços de Integração Hiper-V estão ativados em todos os VM geridos pelo anfitrião. |  [Ativar serviços de integração de hiper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM.<br/><br/> Se estiver a executar o Windows Server 2003, [siga estas instruções](prepare-windows-server-2003-migration.md).
**Credenciais de delegado se os discos VM estiverem localizados em ações remotas de SMB** | O guião delega credenciais. | [Enable CredSSP](#enable-credssp-to-delegate-credentials) para delegar credenciais.

### <a name="run-the-script"></a>Executar o script

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

#### <a name="hashtag-values"></a>Valores de hashtag

Os valores do haxixe são:

| **Haxixe** | **Valor** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Permitir ao CredSSP delegar credenciais

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

1. [Revisão](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Requisitos de hospedeiro hiper-V para migração, e os URLs Azure aos quais os anfitriões e agrupamentos Hiper-V precisam de acesso para a migração de VM.
2. [Reveja](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) os requisitos para Os Hiper-VMs que pretende migrar para Azure.
3. São necessárias algumas alterações nos VM antes de as migrar para Azure.
    - É importante fazer estas mudanças antes de começar a migração. Se migrar o VM antes de fazer a alteração, o VM pode não arrancar em Azure.
    - Reveja as alterações do [Windows](prepare-for-migration.md#windows-machines) e [do Linux](prepare-for-migration.md#linux-machines) que precisa de fazer.



## <a name="next-steps"></a>Próximos passos

Neste tutorial:

> [!div class="checklist"]
> * Confise as permissões da conta Azure.
> * Anfitriões e VM preparados para avaliação e migração.
> * Preparado para a colocação do aparelho Azure Migrate.

Continue até ao próximo tutorial para criar um projeto Azure Migrate, implementar o aparelho e descobrir e avaliar VMs Hiper-V para migração para Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs hiper-V](./tutorial-assess-hyper-v.md)
