---
title: Preparar VMs do Hyper-V para avaliação/migração com as migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs do Hyper-V com migrações para Azure.
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: 1d327f558806e0205540c183c56b92ba31e33cb7
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031225"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para avaliação e migração de VMs do Hyper-V para o Azure

Este artigo descreve como se preparar para avaliação e migração de VMs hiper-V no local para Azure com [Azure Migrate](migrate-services-overview.md).

[A Azure Migrate](migrate-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.

Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do Hyper-V para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure. Configure permissões para sua conta do Azure e recursos para trabalhar com as migrações para Azure.
> * Preparar hosts e VMs do Hyper-V locais para avaliação do servidor. Você pode preparar usando um script de configuração ou manualmente.
> * Prepare-se para a implantação do dispositivo migrações para Azure. O dispositivo é usado para descobrir e avaliar VMs locais.
> * Prepare hosts e VMs locais do Hyper-V para migração de servidor.


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, consulte o How-tos para avaliação e migração do Hyper-V.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões do Azure

Você precisa configurar permissões para a implantação de migrações para Azure.

**Tarefa** | **Permissões**
--- | ---
**Criar um projeto Azure Migrate** | Sua conta do Azure precisa de permissões para criar um projeto.
**Registe o aparelho Azure Migrate** | A Azure Migrate utiliza um aparelho azure migratório leve para descobrir e avaliar vMs Hiper-V com avaliação do servidor migratório Azure. Esse dispositivo descobre VMs e envia metadados de VM e dados de desempenho para migrações para Azure.<br/><br/>Durante o registo do aparelho, os seguintes Fornecedores de Recursos estão registados com a subscrição escolhida no aparelho: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.<br/><br/> Como parte do embarque, a Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD):<br/> A aplicação AAD é utilizada para comunicação (autenticação e autorização) entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. Esta aplicação não tem privilégios para fazer chamadas ARM ou acesso RBAC em qualquer recurso.



### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

Verifique se você tem permissões para criar um projeto de migrações para Azure.

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)** .
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Pode atribuir permissões para a Azure Migrate criar a aplicação Azure AD durante o registo do aparelho, utilizando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo (que tem as permissões) à conta.

> [!NOTE]
> - A aplicação não tem quaisquer outras permissões de acesso na subscrição que não as descritas acima.
> - Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo for configurado.


#### <a name="grant-account-permissions"></a>Permissões de conta de concessão

O locatário/administrador global pode conceder permissões da seguinte maneira:

1. Em Azure AD, o inquilino/administrador global deve navegar para **o Diretório Ativo do Azure** > **utilizadores** > **Definições**de utilizador .
2. O administrador deve definir **os registos** da App para **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>Preparar o Hyper-V para avaliação

Você pode preparar o Hyper-V para a avaliação de VM manualmente ou usando um script de configuração. Aqui está o que precisa ser preparado, quer com o script, quer [manualmente.](#prepare-hyper-v-manually)

- [Verificar](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Configurações de hospedeiro seleção em Hiper-V e certifique-se de que as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos anfitriões Hyper-V.
- Configure a comunicação remota do PowerShell em cada host, para que o dispositivo de migrações para Azure possa executar comandos do PowerShell no host, em uma conexão do WinRM.
- Delegar credenciais se os discos de VM estiverem localizados em compartilhamentos SMB remotos.
- Configure uma conta que será usada pelo dispositivo para descobrir VMs em hosts Hyper-V.
- Configure o Hyper-V Integration Services em cada VM que você deseja descobrir e avaliar.



## <a name="prepare-with-a-script"></a>Preparar com um script

O script faz o seguinte:

- Verifica se você está executando o script em uma versão do PowerShell com suporte.
- Verifica se você (o usuário que está executando o script) tem privilégios administrativos no host Hyper-V.
- Permite que você crie uma conta de usuário local (não administrador) que o serviço migrações para Azure usa para se comunicar com o host Hyper-V. Essa conta de usuário é adicionada a esses grupos no host:
    - Utilizadores de Gestão Remota
    - Administradores hiper-V
    - Utilizadores do Monitor de Desempenho
- Verifica se o host está executando uma versão com suporte do Hyper-V e a função do Hyper-V.
- Habilita o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no host (necessário para a coleta de metadados).
- Habilita a comunicação remota do PowerShell no host.
- Verifica se o serviço de integração do Hyper-V está habilitado em todas as VMs gerenciadas pelo host.
- Habilita CredSSP no host, se necessário.

Execute o script da seguinte maneira:

1. Verifique se você tem o PowerShell versão 4,0 ou posterior instalado no host Hyper-V.
2. Descarregue o script do [Microsoft Download Center](https://aka.ms/migrate/script/hyperv). O script é assinado criptograficamente pela Microsoft.
3. Valide a integridade do script usando os arquivos de hash MD5 ou SHA256. Os valores de hashtag estão abaixo. Execute este comando para gerar o hash para o script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exemplo de uso:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  Depois de validar a integridade do script, execute o script em cada host Hyper-V com este comando do PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Valores de hashtag

Os valores de hash são:

| **Estação Hash** | **Valor** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-hyper-v-manually"></a>Preparar o Hyper-V manualmente

Siga os procedimentos nesta seção para preparar o Hyper-V manualmente, em vez de usar o script.

### <a name="verify-powershell-version"></a>Verificar a versão do PowerShell

Verifique se você tem o PowerShell versão 4,0 ou posterior instalado no host Hyper-V.



### <a name="set-up-an-account-for-vm-discovery"></a>Configurar uma conta para descoberta de VM

As migrações para Azure precisam de permissões para descobrir VMs locais.

- Configure uma conta de usuário local ou de domínio com permissões de administrador nos hosts/cluster do Hyper-V.

    - Você precisa de uma única conta para todos os hosts e clusters que deseja incluir na descoberta.
    - A conta pode ser uma conta local ou de domínio. Recomendamos que ele tenha permissões de administrador nos hosts ou clusters do Hyper-V.
    - Como alternativa, se você não quiser atribuir permissões de administrador, as seguintes permissões serão necessárias:
        - Utilizadores de Gestão Remota
        - Administradores hiper-V
        - Utilizadores do Monitor de Desempenho

### <a name="verify-hyper-v-host-settings"></a>Verificar as configurações do host Hyper-V

1. Verifique os requisitos do [hospedeiro Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) para avaliação do servidor.
2. Certifique-se de que as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos anfitriões Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Habilitar a comunicação remota do PowerShell em hosts

Configure a comunicação remota do PowerShell em cada host, da seguinte maneira:

1. Em cada host, abra um console do PowerShell como administrador.
2. Execute este comando:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Habilitar Integration Services em VMs

Integration Services deve ser habilitado em cada VM para que as migrações para Azure possam capturar informações do sistema operacional na VM.

Em VMs que pretende descobrir e avaliar, permitir [serviços de integração Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM.


### <a name="enable-credssp-on-hosts"></a>Habilitar CredSSP em hosts

Se o host tiver VMs com discos localizados em compartilhamentos SMB, conclua esta etapa no host.

- Você pode executar esse comando remotamente em todos os hosts Hyper-V.
- Se você adicionar novos nós de host em um cluster, eles serão adicionados automaticamente para descoberta, mas você precisará habilitar o CredSSP manualmente nos novos nós, se necessário.

Habilite da seguinte maneira:

1. Identifique os hosts Hyper-V que executam VMs do Hyper-V com discos em compartilhamentos SMB.
2. Execute o seguinte comando em cada host Hyper-V identificado:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Quando montar o aparelho, termine de configurar o CredSSP, [ativando-o no aparelho](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Isso é descrito no próximo tutorial desta série.


## <a name="prepare-for-appliance-deployment"></a>Preparar para a implantação do dispositivo

Antes de configurar o dispositivo migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique os](migrate-appliance.md#appliance---hyper-v) requisitos do aparelho.
2. [Reveja](migrate-appliance.md#url-access) os URLs Azure a que o aparelho terá de aceder.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Note](migrate-appliance.md#collected-data---hyper-v) os requisitos de acesso à porta do aparelho.


## <a name="prepare-for-hyper-v-migration"></a>Preparar para a migração do Hyper-V

1. [Revisão](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Requisitos de hospedeiro hiper-V para migração, e os URLs Azure aos quais os anfitriões e aglomerados Hyper-V precisam de acesso para a migração vm.
2. [Reveja](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) os requisitos para VMs hiper-V que pretende migrar para Azure.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar permissões de conta do Azure.
> * Hosts e VMs do Hyper-V preparados para avaliação e migração.
> * Preparado para implantação do dispositivo migrações para Azure.

Continue no próximo tutorial para criar um projeto de migrações para Azure, implantar o dispositivo e descobrir e avaliar as VMs do Hyper-V para migração para o Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs hiper-V](./tutorial-assess-hyper-v.md)
