---
title: Resolução de problemas do Azure Backup Server
description: Solucionar problemas de instalação, registro de Servidor de Backup do Azure e backup e restauração de cargas de trabalho de aplicativo.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 7fc27a2624fc38883135bdb6d2767625ab02a5a5
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830212"
---
# <a name="troubleshoot-azure-backup-server"></a>Resolução de problemas do Azure Backup Server

Use as informações nas tabelas a seguir para solucionar problemas de erros que você encontrar ao usar Servidor de Backup do Azure.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que você execute a validação abaixo antes de iniciar a solução de problemas do servidor de Backup do Microsoft Azure (MABS):

- [Verifique se o agente de Serviços de Recuperação do Microsoft Azure (MARS) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que existe conectividade de rede entre o agente MARS e o Azure](https://aka.ms/AB-A4dp50)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (na consola do Serviço). Se necessário, reinicie e repita a operação
- [Certifique-se de que 5 a 10% de espaço livre do volume está disponível na localização da pasta de rascunho](https://aka.ms/AB-AA4dwtt)
- Se o registro estiver falhando, verifique se o servidor no qual você está tentando instalar Servidor de Backup do Azure ainda não está registrado com outro cofre
- Se a instalação Push falhar, verifique se o agente DPM já está instalado. Caso esteja, desinstale o agente e volte a tentar a instalação
- [Confirme que nenhum outro processo ou software antivírus está a interferir com o Azure Backup](https://aka.ms/AA4nyr4)<br>
- Certifique-se de que o serviço SQL Agent está em execução e definido como automático no servidor MAB<br>

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre fornecidas inválidas

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Registrando em um cofre | Foram fornecidas credenciais de cofre inválidas. O arquivo está corrompido ou não tem as credenciais mais recentes associadas ao serviço de recuperação. | Ação recomendada: <br> <ul><li> Baixe o arquivo de credenciais mais recente do cofre e tente novamente. <br>OR</li> <li> Se a ação anterior não funcionou, tente baixar as credenciais para um diretório local diferente ou criar um novo cofre. <br>OR</li> <li> Tente atualizar as configurações de data e hora conforme descrito neste [blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>OR</li> <li> Verifique se o c:\Windows\Temp tem mais de 65000 arquivos. Mova os arquivos obsoletos para outro local ou exclua os itens na pasta Temp. <br>OR</li> <li> Verifique o status dos certificados. <br> a. Abra **gerenciar certificados de computador** (no painel de controle). <br> b. Expanda o nó **pessoal** e seus **certificados**de nó filho.<br> c.  Remova o certificado **Ferramentas do Windows Azure**. <br> d. Repita o registro no cliente de backup do Azure. <br> OR </li> <li> Verifique se alguma política de grupo está em vigor. </li></ul> |

## <a name="replica-is-inconsistent"></a>A réplica está inconsistente

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | A réplica está inconsistente | Verifique se a opção verificação de consistência automática no assistente de grupo de proteção está ativada. Para obter mais informações sobre as causas de inconsistência de réplica e sugestões relevantes, consulte o artigo a [réplica está inconsistente](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> No caso do backup de estado do sistema/BMR, verifique se o Backup do Windows Server está instalado no servidor protegido.</li><li> Verifique se há problemas relacionados ao espaço no pool de armazenamento do DPM no servidor DPM/Backup do Microsoft Azure e aloque o armazenamento conforme necessário.</li><li> Verifique o estado do Serviço de Cópias de Sombra de Volume no servidor protegido. Se estiver em um estado desabilitado, defina-o para iniciar manualmente. Inicie o serviço no servidor. Em seguida, volte para o console do DPM/Backup do Microsoft Azure Server e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Falha na criação do ponto de recuperação online

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | Falha na criação do ponto de recuperação online | **Mensagem de erro**: o Windows Azure Backup Agent não pôde criar um instantâneo do volume selecionado. <br> **Solução alternativa**: tente aumentar o espaço na réplica e no volume de pontos de recuperação.<br> <br> **Mensagem de erro**: o agente do Windows Azure backup não pode se conectar ao serviço OBEngine <br> **Solução alternativa**: Verifique se o OBEngine existe na lista de serviços em execução no computador. Se o serviço OBEngine não estiver em execução, use o comando "net start OBEngine" para iniciar o serviço OBEngine. <br> <br> **Mensagem de erro**: a senha de criptografia para este servidor não está definida. Configure uma senha de criptografia. <br> **Solução alternativa**: Tente configurar uma senha de criptografia. Se ele falhar, execute as seguintes etapas: <br> <ol><li>Verifique se o local de rascunho existe. Esse é o local mencionado no registro **HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config**, com o nome **ScratchLocation** deve existir.</li><li> Se o local de rascunho existir, tente registrar novamente usando a frase secreta antiga. *Sempre que você configurar uma senha de criptografia, salve-a em um local seguro.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Os servidores DPM original e externo devem ser registrados no mesmo cofre

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Restauro | **Código de erro**: erro de credenciais de CBPServerRegisteredVaultDontMatchWithCurrent/cofre: 100110 <br/> <br/>**Mensagem de erro**: os servidores DPM originais e externos devem ser registrados no mesmo cofre | **Causa**: esse problema ocorre quando você está tentando restaurar arquivos para o servidor alternativo a partir do servidor original usando a opção de recuperação externa do DPM e se o servidor que está sendo recuperado e o servidor original do qual os dados são copiados não estão associados ao mesmo cofre do serviço de recuperação.<br/> <br/>**Solução alternativa** Para resolver esse problema, certifique-se de que o servidor original e o alternativo estejam registrados no mesmo cofre.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Falha nos trabalhos de criação de ponto de recuperação online para VM VMware

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | Falha nos trabalhos de criação de ponto de recuperação online para VM VMware. O DPM encontrou um erro do VMware ao tentar obter informações de ChangeTracking. ErrorCode-FileFaultFault (ID 33621) |  <ol><li> Redefina o CTK no VMware para as VMs afetadas.</li> <li>Verifique se o disco independente não está em vigor no VMware.</li> <li>Interrompa a proteção para as VMs afetadas e proteja novamente com o botão **Atualizar** . </li><li>Execute um CC para as VMs afetadas.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Falha na operação do agente devido a um erro de comunicação com o serviço coordenador de agentes do DPM no servidor

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Enviando agente (s) para servidores protegidos | Falha na operação do agente devido a um erro de comunicação com o serviço de Coordenador de Agentes do DPM em \<ServerName >. | **Se a ação recomendada mostrada no produto não funcionar, execute as seguintes etapas**: <ul><li> Se você estiver anexando um computador de um domínio não confiável, siga [estas etapas](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> OR </li><li> Se você estiver anexando um computador de um domínio confiável, solucione o problema usando as etapas descritas neste [blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>OR</li><li> Tente desabilitar o antivírus como uma etapa de solução de problemas. Se resolver o problema, modifique as configurações de antivírus conforme sugerido neste [artigo](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A instalação não pôde atualizar os metadados do registro

| Operação | Detalhes do erro | Solução |
|-----------|---------------|------------|
|Instalação | A instalação não pôde atualizar os metadados do registro. Essa falha de atualização pode levar ao superuso do consumo de armazenamento. Para evitar isso, atualize a entrada de registro de aparamento ReFS. | Ajuste a chave do registro **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Defina o valor DWORD como 1. |
|Instalação | A instalação não pôde atualizar os metadados do registro. Essa falha de atualização pode levar ao superuso do consumo de armazenamento. Para evitar isso, atualize a entrada do registro da snapoptimization do volume. | Crie a chave do registro **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** com um valor de cadeia de caracteres vazio. |

## <a name="registration-and-agent-related-issues"></a>Problemas relacionados ao registro e ao agente

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Enviando agente (s) para servidores protegidos | As credenciais especificadas para o servidor são inválidas. | **Se a ação recomendada mostrada no produto não funcionar, execute as seguintes etapas**: <br> Tente instalar o agente de proteção manualmente no servidor de produção, conforme especificado neste [artigo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| O agente de backup do Azure não pôde se conectar ao serviço de backup do Azure (ID: 100050) | O agente de backup do Azure não pôde se conectar ao serviço de backup do Azure. | **Se a ação recomendada mostrada no produto não funcionar, execute as seguintes etapas**: <br>1. Execute o seguinte comando em um prompt com privilégios elevados: **PsExec-i-s "C:\Program Programas\internet Explorer\iexplore.exe**. Isso abre a janela do Internet Explorer. <br/> 2. Acesse **ferramentas** > **opções da Internet** > **conexões** > **configurações de LAN**. <br/> 3. altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<br/> 4. se o seu computador tiver acesso limitado à Internet, verifique se as configurações de firewall no computador ou proxy permitem essas [URLs](backup-configure-vault.md#verify-internet-access) e [endereços IP](backup-configure-vault.md#verify-internet-access).|
| Falha na instalação do agente de backup do Azure | Falha na instalação do Serviços de Recuperação do Microsoft Azure. Todas as alterações feitas no sistema pela instalação do Serviços de Recuperação do Microsoft Azure foram revertidas. (ID: 4024) | Instale manualmente o agente do Azure.

## <a name="configuring-protection-group"></a>Configurando grupo de proteção

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Configurando grupos de proteção | O DPM não pôde enumerar o componente de aplicativo no computador protegido (nome do computador protegido). | Selecione **Atualizar** na tela configurar a interface do usuário do grupo de proteção no nível de DataSource/componente relevante. |
| Configurando grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido for um SQL Server, verifique se as permissões da função sysadmin foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido, conforme descrito neste [artigo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configurando grupos de proteção | Não há espaço livre suficiente no pool de armazenamento para esse grupo de proteção. | Os discos que são adicionados ao pool de armazenamento [não devem conter uma partição](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Exclua todos os volumes existentes nos discos. Em seguida, adicione-os ao pool de armazenamento.|
| Alteração de política |Não foi possível modificar a política de backup. Erro: falha na operação atual devido a um erro de serviço interno [0x29834]. Tente a operação novamente depois que algum tempo tiver passado. Se o problema persistir, contacte o suporte da Microsoft. | **Causa:**<br/>Esse erro ocorre em três condições: quando as configurações de segurança são habilitadas, quando você tenta reduzir o período de retenção abaixo dos valores mínimos especificados anteriormente, e quando você está em uma versão sem suporte. (As versões sem suporte são aquelas abaixo Backup do Microsoft Azure Server versão 2.0.9052 e Servidor de Backup do Azure atualização 1.) <br/>**Ação recomendada:**<br/> Para continuar com as atualizações relacionadas à política, defina o período de retenção acima do período de retenção mínimo especificado. (O período de retenção mínimo é de sete dias para diariamente, quatro semanas para semana, três semanas para mensal ou um ano para o anual.) <br><br>Opcionalmente, outra abordagem preferida é atualizar o agente de backup e Servidor de Backup do Azure para aproveitar todas as atualizações de segurança. |

## <a name="backup"></a>Backup

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | Ocorreu um erro inesperado enquanto o trabalho estava em execução. O dispositivo não está pronto. | **Se a ação recomendada mostrada no produto não funcionar, execute as seguintes etapas:** <br> <ul><li>Defina o espaço de armazenamento de cópia de sombra para ilimitado nos itens do grupo de proteção e execute a verificação de consistência.<br></li> OR <li>Tente excluir o grupo de proteção existente e criar vários novos grupos. Cada novo grupo de proteção deve ter um item individual.</li></ul> |
| Backup | Se você estiver fazendo backup somente do estado do sistema, verifique se há espaço livre suficiente no computador protegido para armazenar o backup do estado do sistema. | <ol><li>Verifique se o Backup do Windows Server está instalado no computador protegido.</li><li>Verifique se há espaço suficiente no computador protegido para o estado do sistema. A maneira mais fácil de verificar isso é ir para o computador protegido, abrir Backup do Windows Server, clicar nas seleções e, em seguida, selecionar BMR. Em seguida, a interface do usuário informa quanto espaço é necessário. Abra o **WSB** > **backup local** > **agendamento de backup** > **selecione configuração de backup** > **servidor completo** (o tamanho é exibido). Use esse tamanho para verificação.</li></ol>
| Backup | Falha de backup para BMR | Se o tamanho de BMR for grande, mova alguns arquivos de aplicativo para a unidade do sistema operacional e tente novamente. |
| Backup | A opção de proteger novamente uma VM do VMware em um novo Backup do Microsoft Azure Server não é mostrada como disponível para adicionar. | As propriedades do VMware são apontadas para uma instância antiga e desativada do Backup do Microsoft Azure Server. Para resolver este problema:<br><ol><li>No VCenter (equivalente a SC-VMM), vá para a guia **Resumo** e, em seguida, para **atributos personalizados**.</li>  <li>Exclua o nome do servidor de Backup do Microsoft Azure antigo do valor **DPMServer** .</li>  <li>Volte para o novo servidor de Backup do Microsoft Azure e modifique o PG.  Depois de selecionar o botão **Atualizar** , a VM é exibida com uma caixa de seleção como disponível para adicionar à proteção.</li></ol> |
| Backup | Erro ao acessar arquivos/pastas compartilhadas | Tente modificar as configurações de antivírus conforme sugerido no artigo do TechNet [executar software antivírus no servidor DPM](https://technet.microsoft.com/library/hh757911.aspx).|

## <a name="change-passphrase"></a>Alterar frase secreta

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Alterar frase secreta |O PIN de segurança que foi inserido está incorreto. Forneça o PIN de segurança correto para concluir esta operação. |**Causa:**<br/> Esse erro ocorre quando você insere um PIN de segurança inválido ou expirado durante a execução de uma operação crítica (como alterar uma frase secreta). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de segurança válido. Para obter o PIN, entre no portal do Azure e vá para o cofre dos serviços de recuperação. Em seguida, vá para **configurações** > **Propriedades** > **gerar PIN de segurança**. Use este PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas ou quando você tenta alterar a frase secreta quando estiver usando uma versão sem suporte.<br/>**Ação recomendada:**<br/> Para alterar a senha, primeiro você deve atualizar o agente de backup para a versão mínima, que é 2.0.9052. Você também precisa atualizar Servidor de Backup do Azure no mínimo da atualização 1 e, em seguida, inserir um PIN de segurança válido. Para obter o PIN, entre no portal do Azure e vá para o cofre dos serviços de recuperação. Em seguida, vá para **configurações** > **Propriedades** > **gerar PIN de segurança**. Use este PIN para alterar a frase secreta. |

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Configurando notificações por email usando uma conta do Office 365 |ID do erro: 2013| **Causa:**<br> Tentando usar a conta do Office 365 <br>**Ação recomendada:**<ol><li> A primeira coisa a garantir é que "permitir retransmissão anônima em um conector de recebimento" para o servidor DPM está configurado no Exchange. Para obter mais informações sobre como configurar isso, consulte [permitir retransmissão anônima em um conector de recebimento](https://technet.microsoft.com/library/bb232021.aspx) no TechNet.</li> <li> Se você não puder usar uma retransmissão de SMTP interna e precisar configurar usando o servidor do Office 365, poderá configurar o IIS para ser uma retransmissão. Configure o servidor DPM para [retransmitir o SMTP para o O365 usando o IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **Importante:** Certifique-se de usar o formato de usuário\@domain.com e *não* Domain\User.<br><br><li>Aponte o DPM para usar o nome do servidor local como servidor SMTP, porta 587. Em seguida, aponte para o email do usuário do qual os emails devem vir.<li> O nome de usuário e a senha na página de instalação do SMTP do DPM devem ser para uma conta de domínio no domínio em que o DPM está. </li><br> **Observação**: quando você estiver alterando o endereço do servidor SMTP, faça a alteração nas novas configurações, feche a caixa configurações e, em seguida, abra-a novamente para ter certeza de que ela reflete o novo valor.  Simplesmente alterar e testar pode nem sempre fazer com que as novas configurações entrem em vigor, de modo que o teste dessa maneira é uma prática recomendada.<br><br>A qualquer momento durante esse processo, você pode limpar essas configurações fechando o console do DPM e editando as seguintes chaves do registro: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> excluir as chaves SMTPPassword e SMTPUserName**. Você pode adicioná-los de volta à interface do usuário ao iniciá-los novamente.

## <a name="common-issues"></a>Problemas comuns

Esta seção aborda os erros comuns que você pode encontrar ao usar Servidor de Backup do Azure.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Mensagem de erro | Ação recomendada |
-- | --
A cópia de segurança falhou porque a réplica da cópia de segurança do disco é inválida ou está em falta. | Para resolver esse problema, verifique as etapas abaixo e repita a operação: <br/> 1. criar um ponto de recuperação de disco<br/> 2. executar verificação de consistência na fonte de origem <br/> 3. interromper a proteção da fonte de dados e reconfigurar a proteção para esta fonte

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo do volume de origem falhou porque os metadados na réplica são inválidos. | Crie um ponto de recuperação de disco dessa fonte de origem e tente novamente o backup online novamente

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Mensagem de erro | Ação recomendada |
-- | --
Falha no instantâneo do volume de origem devido à réplica de DataSource inconsistente. | Execute uma verificação de consistência nessa fonte de origem e tente novamente

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Mensagem de erro | Ação recomendada |
-- | --
A cópia de segurança falhou porque não foi possível clonar a réplica da cópia de segurança do disco.| Verifique se todos os arquivos de réplica de backup de disco anteriores (. vhdx) estão desmontados e se não há backup de disco para disco em andamento durante os backups online
