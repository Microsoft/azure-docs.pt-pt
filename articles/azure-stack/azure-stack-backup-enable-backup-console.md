---
title: Ativar cópia de segurança para o Azure Stack a partir do portal de administração | Documentos da Microsoft
description: Ative o serviço de cópia de segurança de infraestrutura através do portal de administração para que o Azure Stack pode ser restaurado se ocorrer uma falha.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 98f793b7d94cd554d426a0eec30d8bb4553d3d81
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105408"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Ativar cópia de segurança para o Azure Stack a partir do portal de administração
Ative o serviço de cópia de segurança de infraestrutura através do portal de administração para que o Azure Stack pode gerar cópias de segurança da infraestrutura. O parceiro de hardware pode utilizar estas cópias de segurança para restaurar o seu ambiente na cloud de recuperação na eventualidade de a utilizar [uma falha catastrófica](./azure-stack-backup-recover-data.md). O objetivo de recuperação na cloud é garantir que seus operadores e os utilizadores podem iniciar sessão no portal do após a conclusão da recuperação. Os utilizadores terão suas assinaturas restauradas, incluindo permissões de acesso baseado em funções e funções, originais planos, ofertas e computação definida anteriormente, armazenamento, quotas de rede e segredos do Key Vault.

No entanto, o serviço de cópia de segurança de infraestrutura não fazer uma cópia de segurança de VMs de IaaS, configurações de rede e recursos de armazenamento como contas de armazenamento, blobs, tabelas e assim por diante, para que os utilizadores iniciar sessão após a conclusão da recuperação na cloud não verão qualquer um dos anteriormente existente recursos. Plataforma como serviço (PaaS) recursos e os dados são também não backup pelo serviço. 

Os administradores e utilizadores são responsáveis por fazer backup e restaurar os recursos de IaaS e PaaS separadamente dos processos de cópia de segurança da infraestrutura. Para obter informações sobre cópias de segurança recursos de IaaS e PaaS, consulte as seguintes ligações:

- [Máquinas Virtuais](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [Serviço de Aplicações](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Ativar ou reconfigurar a cópia de segurança

1. Abra o [portal de administração do Azure Stack](azure-stack-manage-portals.md).
2. Selecione **todos os serviços**e, no **administração** selecione categoria **cópia de segurança da infraestrutura**. Escolher **Configuration** no **cópia de segurança da infraestrutura** painel.
3. Escreva o caminho para o **localização de armazenamento de cópia de segurança**. Utilize uma cadeia de caracteres de convenção de Nomenclatura Universal (UNC) para o caminho para uma partilha de ficheiros hospedado num dispositivo separado. Uma cadeia de caracteres UNC Especifica a localização de recursos, tais como ficheiros partilhados ou dispositivos. Para o serviço, pode utilizar um endereço IP. Para garantir a disponibilidade dos dados de cópia de segurança após um desastre, o dispositivo deve estar num local separado.

    > [!Note]  
    > Se o ambiente oferecer suporte a resolução do nome de rede de infraestrutura do Azure Stack para seu ambiente empresarial, pode utilizar um FQDN, em vez do IP.

4. Tipo de **nome de utilizador** usando o domínio e nome de utilizador com acesso suficiente para ler e escrever ficheiros. Por exemplo, `Contoso\backupshareuser`.
5. Tipo de **palavra-passe** para o utilizador.
6. Escreva a palavra-passe novamente ao **Confirmar palavra-passe**.
7. O **frequência em horas** determina com que frequência as cópias de segurança são criadas. O valor predefinido é 12. O Scheduler suporta um máximo de 12 e um mínimo de 4. 
8. O **período de retenção em dias** determina o número de dias de cópias de segurança é mantido na localização externa. O valor predefinido é de 7. O Scheduler suporta um máximo de 14 e um mínimo de 2. As cópias de segurança mais antigas do que o período de retenção são automaticamente eliminadas da localização externa.

    > [!Note]  
    > Se pretende arquivar as cópias de segurança mais antigas do que o período de retenção, lembre-se de que os ficheiros de cópia de segurança antes do agendador elimina as cópias de segurança. Se reduzir o período de retenção de cópia de segurança (por exemplo, a partir de 7 dias para 5 dias), o agendador eliminará todas as cópias de segurança mais antigas do que o novo período de retenção. Certifique-se de que tiver problemas com as cópias de segurança introdução eliminadas antes de atualizar este valor. 

9. Nas definições de encriptação de fornecer um certificado na caixa de ficheiro. cer de certificado. Ficheiros de cópia de segurança são encriptados com esta chave pública no certificado. Deve fornecer um certificado que contém apenas a parte de chave pública quando configurar as definições de cópia de segurança. Depois de definir este certificado pela primeira vez ou girar o certificado no futuro, pode ver apenas o thumbprint do certificado. Não é possível transferir ou ver o ficheiro de certificado carregado. Para criar o ficheiro de certificado, execute o seguinte comando do PowerShell para criar um certificado autoassinado com as chaves públicas e privadas e exportar um certificado com somente a parte de chave pública.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 e acima**: O Azure Stack aceita um certificado para encriptar os dados de cópia de segurança de infra-estrutura. Certifique-se de que pretende armazenar o certificado com a chave pública e privada numa localização segura. Por motivos de segurança, não é recomendado que utilize o certificado com as chaves públicas e privadas para configurar as definições de cópia de segurança. Para obter mais informações sobre como gerir o ciclo de vida deste certificado, consulte [melhores práticas do serviço de cópia de segurança de infra-estrutura](azure-stack-backup-best-practices.md).
   > 
   > **1811 ou anterior**: O Azure Stack aceita uma chave simétrica para criptografar dados de cópia de segurança de infra-estrutura. Utilize o [AzsEncryptionKey64 novo cmdlet para criar uma chave](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Depois de atualizar a partir de 1811 1901, definições de cópia de segurança irão reter a chave de encriptação. Recomenda-se ao atualizar as definições de cópia de segurança para utilizar um certificado. Suporte de chave de encriptação foi agora preterido. Terá pelo menos 3 versões ao atualizar as definições para utilizar um certificado. 

10. Selecione **OK** para guardar as definições de cópia de segurança do controlador.

![O Azure Stack - definições do controlador de cópia de segurança](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Iniciar cópia de segurança
Para iniciar uma cópia de segurança, clique em **agora a cópia de segurança** para iniciar uma cópia de segurança a pedido. Uma cópia de segurança a pedido não irá modificar a hora para o próximo backup agendado. Depois da tarefa estiver concluída, pode confirmar as definições no **Essentials**:

![O Azure Stack - cópia de segurança a pedido](media/azure-stack-backup/scheduled-backup.png)

Também pode executar o cmdlet do PowerShell **Start-AzsBackup** no seu computador de administração do Azure Stack. Para obter mais informações, consulte [cópia de segurança do Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Ativar ou desativar as cópias de segurança automáticas
As cópias de segurança agendadas automaticamente ao ativar a cópia de segurança. Pode verificar a próxima hora de cópia de segurança agendada na **Essentials**. 

![O Azure Stack - cópia de segurança a pedido](media/azure-stack-backup/on-demand-backup.png)

Se tiver de desativar futuras cópias de segurança agendadas, clique em **desativar cópias de segurança automáticas**. Desativar as cópias de segurança automática irão manter as definições de cópia de segurança configuradas e irão reter a agenda de cópia de segurança. Esta ação simplesmente diz ao scheduler para ignorar futuras cópias de segurança. 

![O Azure Stack - desativar agendado cópias de segurança](media/azure-stack-backup/disable-auto-backup.png)

Confirme que futuras cópias de segurança agendadas foram desativadas na **Essentials**:

![O Azure Stack - confirmar as cópias de segurança foram desativadas](media/azure-stack-backup/confirm-disable.png)

Clique em **habilitar cópias de segurança automáticas** para informar o scheduler para iniciar cópias de segurança futuras no horário programado. 

![O Azure Stack - ativar agendado cópias de segurança](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Se tiver configurado a cópia de segurança da infraestrutura antes de atualizar para 1807, cópias de segurança automáticas serão desativadas. Desta forma as cópias de segurança iniciadas pelo Azure Stack não entram em conflito com cópias de segurança iniciadas por uma motor de agendamento de tarefas externas. Depois de desativar qualquer programador de tarefas externa, clique em **habilitar cópias de segurança automáticas**.

## <a name="update-backup-settings"></a>Atualizar as definições de cópia de segurança
No momento da elaboração 1901, o suporte para a chave de encriptação foi preterida. Se estiver a configurar cópia de segurança pela primeira vez no 1901, tem de utilizar um certificado. O Azure Stack oferece suporte a chave de encriptação apenas se a chave é configurada antes de atualizar para 1901. Modo de compatibilidade com versões anteriores irá continuar para três versões. Depois disso, as chaves de encriptação já não ser suportadas. 

### <a name="default-mode"></a>Modo predefinido
Nas definições de encriptação, se estiver a configurar a cópia de segurança da infraestrutura pela primeira vez após a instalação ou atualização para 1901, tem de configurar cópia de segurança com um certificado. Já não é suportada a utilização de uma chave de encriptação. 

Para atualizar o certificado utilizado para encriptar dados da cópia de segurança, pode carregar um novo. CER de ficheiros com a parte de chave pública e selecione OK para guardar as definições. 

Novas cópias de segurança irão começar a utilizar a chave pública no certificado novo. Não há nenhum impacto sobre a todas as cópias de segurança existentes criados com o certificado anterior. Certifique-se manter o certificado mais antigo em torno numa localização segura no caso de ser necessário para recuperação na nuvem.

![O Azure Stack - thumbprint do certificado de vista](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Efeitos modo de compatibilidade
Se tiver configurado a cópia de segurança antes de atualizar para 1901, as definições são passadas com nenhuma alteração no comportamento. Neste caso, em que a chave de encriptação é suportada com versões anteriores para compatibilidade. Tem a opção de atualizar a chave de encriptação ou mudar para utilizar um certificado. Terá de, pelo menos, três versões para continuar a atualizar a chave de encriptação. Utilize este tempo, a transição para um certificado. Para criar uma nova utilização de chave de encriptação a [cmdlet New-AzsEncryptionKeyBase64](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![O Azure Stack - com a chave de encriptação no modo de compatibilidade com versões anteriores](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> A atualizar a partir da chave de encriptação para o certificado é uma operação unidirecional. Depois de fazer esta alteração, não é possível voltar a mudar para a chave de encriptação. Todas as cópias de segurança existentes permanecerão encriptadas com a chave de encriptação anteriores. 

![O Azure Stack - utilizar o certificado de encriptação no modo de compatibilidade com versões anteriores](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Passos Seguintes

Aprenda a executar uma cópia de segurança. Consulte [cópia de segurança do Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Saiba como verificar se a cópia de segurança executou. Consulte [Confirmar cópia de segurança foi concluída no portal de administração](azure-stack-backup-back-up-azure-stack.md)
