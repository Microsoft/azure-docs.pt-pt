---
title: Preparar VMs do Hyper-V para avaliação/migração com as migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs do Hyper-V com migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f93528e2a35661f8a233aea476a958a079d7cd59
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196267"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para avaliação e migração de VMs do Hyper-V para o Azure

Este artigo descreve como preparar a avaliação e a migração de VMs do Hyper-V locais para o Azure com as [migrações para Azure](migrate-services-overview.md).

As [migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.

Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do Hyper-V para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure. Configure permissões para sua conta do Azure e recursos para trabalhar com as migrações para Azure.
> * Preparar hosts e VMs do Hyper-V locais para avaliação do servidor.
> * Prepare hosts e VMs locais do Hyper-V para migração de servidor.


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, consulte o How-tos para avaliação e migração do Hyper-V.


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


## <a name="prepare-for-hyper-v-assessment"></a>Preparar para a avaliação do Hyper-V

Para se preparar para a avaliação do Hyper-V, faça o seguinte:

1. Verifique as configurações do host Hyper-V.
2. Configure a comunicação remota do PowerShell em cada host, para que o dispositivo de migrações para Azure possa executar comandos do PowerShell no host, em uma conexão do WinRM.
3. Se os discos de VM estiverem localizados no armazenamento SMB remoto, a delegação de credenciais será necessária.
    - Habilite a delegação de CredSSP para que o dispositivo de migrações para Azure possa atuar como o cliente, delegando credenciais a um host.
    - Você permite que cada host atue como um delegado para o dispositivo, conforme descrito abaixo.
    - Posteriormente, ao configurar o dispositivo, você habilitará a delegação no dispositivo.
4. Examine os requisitos do dispositivo e o acesso à URL/porta necessário para o dispositivo.
5. Configure uma conta que será usada pelo dispositivo para descobrir VMs.
6. Configure o Hyper-V Integration Services em cada VM que você deseja descobrir e avaliar.


Você pode definir essas configurações manualmente usando os procedimentos a seguir. Como alternativa, você executa o script de configuração de pré-requisitos do Hyper-V.

### <a name="hyper-v-prerequisites-configuration-script"></a>Script de configuração de pré-requisitos do Hyper-V

O script valida os hosts Hyper-V e define as configurações necessárias para descobrir e avaliar as VMs do Hyper-V. Veja o que ele faz:

- Verifica se você está executando o script em uma versão do PowerShell com suporte.
- Verifica se você (o usuário que está executando o script) tem privilégios administrativos no host Hyper-V.
- Permite que você crie uma conta de usuário local (não administrador) que é usada para o serviço migrações para Azure se comunicar com o host Hyper-V. Essa conta de usuário é adicionada a esses grupos no host:
    - Usuários de gerenciamento remoto
    - Administradores do Hyper-V
    - Usuários do monitor de desempenho
- Verifica se o host está executando uma versão com suporte do Hyper-V e a função do Hyper-V.
- Habilita o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no host (necessário para a coleta de metadados).
- Habilita a comunicação remota do PowerShell no host.
- Verifica se o serviço de integração do Hyper-V está habilitado em todas as VMs gerenciadas pelo host.
- Habilita CredSSP no host, se necessário.

Execute o script da seguinte maneira:

1. Verifique se você tem o PowerShell versão 4,0 ou posterior instalado no host Hyper-V.
2. Baixe o script do [centro de download da Microsoft](https://aka.ms/migrate/script/hyperv). O script é assinado criptograficamente pela Microsoft.
3. Valide a integridade do script usando arquivos de hash MD5 ou SHA256. Os valores de hashtag estão abaixo. Execute este comando para gerar o hash para o script:
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

#### <a name="hashtag-values"></a>Valores de hashtag

Os valores de hash são:

| **Tralha** | **Valor** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

### <a name="verify-hyper-v-host-settings"></a>Verificar as configurações do host Hyper-V

1. Verifique [os requisitos de host do Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) para avaliação do servidor.
2. Verifique se as [portas necessárias](migrate-support-matrix-hyper-v.md#assessment-port-requirements) estão abertas em hosts Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Habilitar a comunicação remota do PowerShell em hosts

Configure a comunicação remota do PowerShell em cada host, da seguinte maneira:

1. Em cada host, abra um console do PowerShell como administrador.
2. Execute este comando:

    ```
    Enable-PSRemoting -force
    ```

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

Ao configurar o dispositivo, você conclui a configuração do CredSSP [habilitando-o no dispositivo](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Isso é descrito no próximo tutorial desta série.


### <a name="verify-appliance-settings"></a>Verificar as configurações do dispositivo

Antes de configurar o dispositivo migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) os requisitos do dispositivo.
2. [Examine](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) as URLs do Azure que o dispositivo precisará acessar.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Anote](migrate-support-matrix-hyper-v.md#assessment-port-requirements) os requisitos de acesso da porta para o dispositivo.


### <a name="set-up-an-account-for-vm-discovery"></a>Configurar uma conta para descoberta de VM

As migrações para Azure precisam de permissões para descobrir VMs locais.

- Configure uma conta de usuário local ou de domínio com permissões de administrador nos hosts/cluster do Hyper-V.

    - Você precisa de uma única conta para todos os hosts e clusters que deseja incluir na descoberta.
    - A conta pode ser uma conta local ou de domínio. Recomendamos que ele tenha permissões de administrador nos hosts ou clusters do Hyper-V.
    - Como alternativa, se você não quiser atribuir permissões de administrador, as seguintes permissões serão necessárias:
        - Usuários de gerenciamento remoto
        - Administradores do Hyper-V
        - Usuários do monitor de desempenho

### <a name="enable-integration-services-on-vms"></a>Habilitar Integration Services em VMs

Integration Services deve ser habilitado em cada VM para que as migrações para Azure possam capturar informações do sistema operacional na VM.

Nas VMs que você deseja descobrir e avaliar, habilite o [Hyper-V Integration Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM.

## <a name="prepare-for-hyper-v-migration"></a>Preparar para a migração do Hyper-V

1. [Examinar](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Requisitos de host do Hyper-V para migração.
2. [Examine](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) os requisitos para VMs do Hyper-V que você deseja migrar para o Azure.
3. [Observe](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) as URLs do Azure às quais os hosts e clusters do Hyper-V precisam de acesso para a migração da VM.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar permissões de conta do Azure.
> * Hosts e VMs do Hyper-V preparados para avaliação e migração.

Continue no próximo tutorial para criar um projeto de migrações para Azure e avaliar as VMs do Hyper-V para migração para o Azure

> [!div class="nextstepaction"]
> [Avaliar VMs do Hyper-V](./tutorial-assess-hyper-v.md)
