---
title: Resolução de problemas do Azure Backup Server
description: Instalação de resolução de problemas, registo do Servidor de Backup Azure e cópia de segurança e restauro de cargas de trabalho de aplicação.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 7a1cac63ba6497b8580c83fe2b666b020701283a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81688039"
---
# <a name="troubleshoot-azure-backup-server"></a>Resolução de problemas do Azure Backup Server

Utilize as informações nas seguintes tabelas para resolver erros que encontra ao utilizar o Servidor de Backup Azure.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que execute a validação abaixo, antes de começar a resolver problemas no Microsoft Azure Backup Server (MABS):

- [Certifique-se de que o Agente dos Serviços de Recuperação do Microsoft Azure (MARS) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que existe conectividade de rede entre o agente MARS e o Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (na consola do Serviço). Se necessário, reinicie e tente novamente a operação
- [Certifique-se de que 5 a 10% de espaço livre do volume está disponível na localização da pasta de rascunho](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Se o registo estiver a falhar, certifique-se de que o servidor em que está a tentar instalar o Servidor de Backup Do Azure ainda não está registado com outro cofre.
- Se a instalação Push falhar, verifique se o agente DPM já está instalado. Caso esteja, desinstale o agente e volte a tentar a instalação
- [Confirme que nenhum outro processo ou software antivírus está a interferir com o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Certifique-se de que o serviço SQL Agent está a funcionar e definido para automático no servidor MABS<br>

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Registrando-se em um cofre | Foram fornecidas credenciais de cofre inválidas. O ficheiro é corrompido ou não tem as últimas credenciais associadas ao serviço de recuperação. | Ação recomendada: <br> <ul><li> Descarregue o ficheiro de credenciais mais recentes do cofre e tente de novo. <br>(OU)</li> <li> Se a ação anterior não funcionou, tente transferir as credenciais para um diretório local diferente ou crie um novo cofre. <br>(OU)</li> <li> Tente atualizar as definições de data e hora descritas [neste artigo](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided). <br>(OU)</li> <li> Verifique se c:\windows\tem mais de 65000 ficheiros. Desloque os ficheiros velhos para outro local ou elimine os itens da pasta Temp. <br>(OU)</li> <li> Verifique o estado dos certificados. <br> a. **Open Manage Certificados de computador** (no Painel de Controlo). <br> b. Expandir o nó **pessoal** e os **certificados**de nó infantil.<br> c.  Remova o certificado **Ferramentas Windows Azure**. <br> d. Retry o registo no cliente Azure Backup. <br> (OU) </li> <li> Verifique se existe alguma política de grupo. </li></ul> |

## <a name="replica-is-inconsistent"></a>A réplica é inconsistente

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | A réplica é inconsistente | Verifique se a opção de verificação automática de consistência no Assistente do Grupo de Proteção está ligada. Para obter mais informações sobre as causas da inconsistência da réplica e sugestões relevantes, consulte o artigo [Replica é inconsistente](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10)).<br> <ol><li> No caso de cópia de segurança System State/BMR, verifique se a Cópia de Segurança do Servidor do Windows está instalada no servidor protegido.</li><li> Verifique se há problemas relacionados com o espaço no depósito do DPM no Servidor de Backup DPM/Microsoft Azure e aloque o armazenamento conforme necessário.</li><li> Verifique o estado do Serviço de Cópia de Sombra de Volume no servidor protegido. Se estiver em estado de desativação, desative-o para começar manualmente. Inicie o serviço no servidor. Em seguida, volte para a consola DPM/Microsoft Azure Backup Server e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Falha na criação de ponto de recuperação online

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | Falha na criação de ponto de recuperação online | **Error Message**: O Agente de Backup Windows Azure não conseguiu criar uma imagem do volume selecionado. <br> **Suposição :** Tente aumentar o espaço no volume do ponto de replicação e recuperação.<br> <br> **Error Message**: O Agente de Backup Windows Azure não pode ligar-se ao serviço OBEngine <br> **Seleção**: verifique se o OBEngine existe na lista de serviços de funcionamento no computador. Se o serviço OBEngine não estiver em funcionamento, utilize o comando "net start OBEngine" para iniciar o serviço OBEngine. <br> <br> **Error Message**: A frase-passe de encriptação para este servidor não está definida. Por favor, configure uma frase de encriptação. <br> **Suposição :** Tente configurar uma frase de encriptação. Se falhar, tome os seguintes passos: <br> <ol><li>Verifique se a localização do risco existe. Esta é a localização que é mencionada no registo **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config,** com o nome **ScratchLocation** deve existir.</li><li> Se a localização do risco existir, tente reregistar-se utilizando a antiga frase-passe. *Sempre que configurar uma frase de encriptação, guarde-a num local seguro.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Os servidores DPM originais e externos devem ser registados no mesmo cofre

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Restauro | **Código de erro**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Mensagem de erro**: Os servidores DPM originais e externos devem ser registados no mesmo cofre | **Causa**: Este problema ocorre quando está a tentar restaurar ficheiros no servidor alternativo a partir do servidor original utilizando a opção de recuperação externa do DPM e se o servidor que está a ser recuperado e o servidor original de onde os dados são apoiados não estão associados ao mesmo cofre do Serviço de Recuperação.<br/> <br/>**Supor** Para resolver este problema, certifique-se de que o servidor original e alternativo está registado no mesmo cofre.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Falhas na criação de pontos de recuperação online para VMware VM

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | Os postos de trabalho de criação de pontos de recuperação online para vMware VM falham. O DPM encontrou um erro da VMware enquanto tentava obter informações do ChangeTracking. ErrorCode - ErrorFaultFault (ID 33621) |  <ol><li> Redefinir o CTK em VMware para os VM afetados.</li> <li>Verifique se o disco independente não está no lugar da VMware.</li> <li>Pare a proteção dos VMs afetados e reproteja com o botão **Refresh.** </li><li>Execute um CC para os VMs afetados.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>A operação do agente falhou devido a um erro de comunicação com o serviço de coordenador do agente DPM no servidor

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Empurrar agentes para servidores protegidos | A operação do agente falhou devido a um erro \<de comunicação com o serviço de Coordenador do Agente DPM no ServidorName>. | **Se a ação recomendada no produto não funcionar, então execute os seguintes passos:** <ul><li> Se estiver a ligar um computador a partir de um domínio não confiável, siga [estes passos](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019). <br> (OU) </li><li> Se estiver a anexar um computador a partir de um domínio de confiança, saque de problemas utilizando os passos descritos [neste blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>(OU)</li><li> Tente desativar o antivírus como um passo de resolução de problemas. Se resolver o problema, modifique as definições antivírus, tal como sugerido [neste artigo](https://docs.microsoft.com/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A configuração não conseguiu atualizar os metadados de registo

| Operação | Detalhes do erro | Solução |
|-----------|---------------|------------|
|Instalação | A configuração não conseguiu atualizar os metadados do registo. Esta falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar esta atualização, a entrada do registo ReFS Trimming. | Ajuste a chave de registo **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Desloque o valor Dword para 1. |
|Instalação | A configuração não conseguiu atualizar os metadados do registo. Esta falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isto, atualize a entrada do registo Volume SnapOptimization. | Crie a chave de registo **SOFTWARE\Microsoft Data Protection Manager\Configuração\VolSnapOptimization\WriteIds** com um valor de cadeia vazio. |

## <a name="registration-and-agent-related-issues"></a>Questões relacionadas com o registo e agentes

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Empurrar agentes para servidores protegidos | As credenciais especificadas para o servidor são inválidas. | **Se a ação recomendada que é mostrada no produto não funcionar, tome as seguintes medidas:** <br> Tente instalar o agente de proteção manualmente no servidor de produção, conforme especificado [neste artigo](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).|
| O Agente de Backup Azure não conseguiu ligar-se ao serviço de backup Azure (ID: 100050) | O Agente de Apoio Azure não conseguiu ligar-se ao serviço de backup Azure. | **Se a ação recomendada que é mostrada no produto não funcionar, tome as seguintes medidas:** <br>1. Executar o seguinte comando a partir de um pedido elevado: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Isto abre a janela do Internet Explorer. <br/> 2. Ir às **ferramentas** >  > **Opções** > de Internet**Ligações****LAN definições**. <br/> 3. Altere as definições para utilizar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<br/> 4. Se a sua máquina tiver acesso limitado à Internet, certifique-se de que as definições de firewall na máquina ou proxy permitem estes [URLs](install-mars-agent.md#verify-internet-access) e [endereço IP](install-mars-agent.md#verify-internet-access).|
| Instalação do Agente de Backup Azure falhou | A instalação dos Serviços de Recuperação do Microsoft Azure falhou. Todas as alterações que foram feitas no sistema pela instalação dos Serviços de Recuperação do Microsoft Azure foram revertidas. (ID: 4024) | Instale manualmente o Agente Azure.

## <a name="configuring-protection-group"></a>Grupo de proteção de configuração

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Configuração de grupos de proteção | O DPM não conseguiu enumerar o componente de aplicação no computador protegido (nome de computador protegido). | Selecione **Refresh** no ecrã UI do grupo de proteção configurar no nível de fonte de dados/componente relevante. |
| Configuração de grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido for um servidor SQL, verifique se as permissões de função sysadmin foram fornecidas à conta do sistema (NTAuthority\System) no computador protegido, conforme descrito [neste artigo](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12)).
| Configuração de grupos de proteção | Não há espaço livre suficiente na piscina de armazenamento para este grupo de proteção. | Os discos adicionados à piscina de armazenamento [não devem conter uma divisória](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)). Elimine os volumes existentes nos discos. Em seguida, adicione-os à piscina de armazenamento.|
| Mudança de política |A política de apoio não podia ser modificada. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Por favor, tente novamente a operação depois de algum tempo. Se o problema persistir, contacte o suporte da Microsoft. | **Causa:**<br/>Este erro ocorre em três condições: quando as definições de segurança são ativadas, quando se tenta reduzir o intervalo de retenção abaixo dos valores mínimos especificados anteriormente e quando se encontra numa versão não suportada. (Versões não suportadas são as abaixo da versão 2.0.9052 do Microsoft Azure Backup Server e da atualização do Servidor de Backup Azure 1.) <br/>**Ação recomendada:**<br/> Para proceder a atualizações relacionadas com as políticas, estabeleça o período de retenção acima do período mínimo de retenção especificado. (O período mínimo de retenção é de sete dias para o dia-a-dia, quatro semanas para semana, três semanas para o mês ou um ano para o ano.) <br><br>Opcionalmente, outra abordagem preferida é atualizar o agente de backup e o Servidor de Backup Azure para alavancar todas as atualizações de segurança. |

## <a name="backup"></a>Cópia de segurança

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | Ocorreu um erro inesperado enquanto o trabalho estava a decorrer. O dispositivo não está pronto. | **Se a ação recomendada que é mostrada no produto não funcionar, tome as seguintes medidas:** <br> <ul><li>Delineie o espaço de armazenamento de cópias-sombra para ilimitado nos itens do grupo de proteção e, em seguida, faça a verificação de consistência.<br></li> (OU) <li>Tente eliminar o grupo de proteção existente e criar vários novos grupos. Cada novo grupo de proteção deve ter um item individual nele.</li></ul> |
| Cópia de segurança | Se estiver a apoiar apenas o estado do sistema, verifique se há espaço livre suficiente no computador protegido para armazenar a cópia de segurança do estado do sistema. | <ol><li>Verifique se o Windows Server Backup está instalado na máquina protegida.</li><li>Verifique se há espaço suficiente no computador protegido para o estado do sistema. A maneira mais fácil de verificar isto é ir ao computador protegido, abrir o Windows Server Backup, clicar nas seleções e, em seguida, selecionar BMR. A UI então diz-lhe quanto espaço é necessário. Abra o**plano** > **de backup** > local **WSB** > **Selecione configuração** > **completa de** backup (tamanho é apresentado). Utilize este tamanho para verificação.</li></ol>
| Cópia de segurança | Falha de back up para BMR | Se o tamanho BMR for grande, mova alguns ficheiros de aplicação para a unidade de S e tente novamente. |
| Cópia de segurança | A opção de reproteger um VMware VM num novo Microsoft Azure Backup Server não mostra como disponível para adicionar. | As propriedades vMware são apontadas para um antigo caso aposentado do Microsoft Azure Backup Server. Para resolver este problema:<br><ol><li>No VCenter (equivalente SC-VMM), vá ao separador **Resumo** e, em seguida, a **Atributos Personalizados**.</li>  <li>Elimine o antigo nome do Microsoft Azure Backup Server do valor **DPMServer.**</li>  <li>Volte ao novo Servidor de Backup Microsoft Azure e modifique o PG.  Depois de selecionar o botão **Refresh,** o VM aparece com uma caixa de verificação disponível para adicionar à proteção.</li></ol> |
| Cópia de segurança | Erro ao aceder a ficheiros/pastas partilhadas | Tente modificar as definições antivírus, tal como sugerido neste artigo, [executar software antivírus no servidor DPM](https://docs.microsoft.com/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).|

## <a name="change-passphrase"></a>Alterar a frase-passe

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Alterar a frase-passe |O PIN de segurança que foi introduzido está incorreto. Forneça o PIN de segurança correto para completar esta operação. |**Causa:**<br/> Este erro ocorre quando introduz um PIN de segurança inválido ou expirado enquanto está a executar uma operação crítica (como alterar uma frase de passe). <br/>**Ação recomendada:**<br/> Para completar a operação, deve introduzir um PIN de segurança válido. Para obter o PIN, inscreva-se no portal Azure e vá ao cofre dos Serviços de Recuperação. Em seguida, vá para **Definições** > **Propriedades** > **Geram PIN**de Segurança . Utilize este PIN para alterar a frase-passe. |
| Alterar a frase-passe |A operação falhou. ID: 120002 |**Causa:**<br/>Este erro ocorre quando as definições de segurança estão ativadas, ou quando tenta alterar a palavra-passe quando estiver a utilizar uma versão não suportada.<br/>**Ação recomendada:**<br/> Para alterar a palavra-passe, tem primeiro de atualizar o agente de backup para a versão mínima, que é de 2.0.9052. Também precisa de atualizar o Servidor de Backup Azure ao mínimo de atualização 1 e, em seguida, introduzir um PIN de segurança válido. Para obter o PIN, assine no portal Azure e vá ao cofre dos Serviços de Recuperação. Em seguida, vá para **Definições** > **Propriedades** > **Geram PIN**de Segurança . Utilize este PIN para alterar a frase-passe. |

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Configuração de notificações por e-mail utilizando uma conta do Office 365 |ID de erro: 2013| **Causa:**<br> Tentando usar a conta do Office 365 <br>**Ação recomendada:**<ol><li> A primeira coisa a garantir é que "Permitir retransmissão anónima num Conector de Receção" para o seu servidor DPM está configurado no Exchange. Para mais informações sobre como configurar isto, consulte Permitir a [Retransmissão Anónima num Conector de Receção](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019).</li> <li> Se não puder utilizar um relé SMTP interno e precisar de configurar utilizando o seu servidor Office 365, pode configurar o IIS para ser um retransmissor. Configure o servidor DPM para [retransmitir o SMTP para O365 utilizando o IIS](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)).<br><br>  Certifique-se de\@utilizar o formato domain.com utilizador e *não* o utilizador do domínio.utilizador.<br><br><li>Ponto DPM para usar o nome do servidor local como servidor SMTP, porta 587. Em seguida, aponte para o e-mail do utilizador de que os e-mails devem vir.<li> O nome de utilizador e a palavra-passe na página de configuração DPM SMTP devem ser para uma conta de domínio no domínio em que o DPM está ligado. </li><br> Quando estiver a alterar o endereço do servidor SMTP, faça a alteração para as novas definições, feche a caixa de definições e, em seguida, reabra-a para ter a certeza de que reflete o novo valor.  A simples alteração e teste pode nem sempre fazer com que as novas definições entrem em vigor, pelo que testá-la desta forma é uma boa prática.<br><br>Em qualquer momento durante este processo, pode limpar estas definições fechando a consola DPM e editando as seguintes teclas de registo: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword e SMTPUserName .** Pode adicioná-los de volta à UI quando o lançar de novo.

## <a name="common-issues"></a>Problemas comuns

Esta secção cobre os erros comuns que poderá encontrar ao utilizar o Servidor de Backup Azure.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Mensagem de erro | Ação recomendada |
-- | --
A cópia de segurança falhou porque a réplica da cópia de segurança do disco é inválida ou está em falta. | Para resolver este problema, verifique os passos abaixo e tente novamente a operação: <br/> 1. Criar um ponto de recuperação do disco<br/> 2. Verifique a consistência do prazo no datasource <br/> 3. Parar a proteção da fonte de dados e, em seguida, reconfigurar a proteção para esta fonte de dados

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo do volume de origem falhou porque os metadados na réplica são inválidos. | Crie um ponto de recuperação de disco desta fonte de dados e volte a tentar a cópia de segurança online

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo do volume de origem falhou devido a uma réplica inconsistente da fonte de dados. | Faça uma verificação de consistência nesta fonte de dados e tente novamente

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Mensagem de erro | Ação recomendada |
-- | --
A cópia de segurança falhou porque não foi possível clonar a réplica da cópia de segurança do disco.| Certifique-se de que todos os ficheiros de réplica de cópia de segurança de disco anteriores (.vhdx) estão desmontados e que não está em curso qualquer disco para a cópia de segurança do disco durante as cópias de segurança online
