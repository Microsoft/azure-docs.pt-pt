---
title: Resolução de problemas do Azure Backup Server
description: Resolver problemas de instalação, o registo de servidor de cópia de segurança do Azure e a cópia de segurança e restauro das cargas de trabalho de aplicação.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: kasinh
ms.openlocfilehash: 22507a1b89c6a7d6867e9b669e1a2e70106a4e41
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880573"
---
# <a name="troubleshoot-azure-backup-server"></a>Resolução de problemas do Azure Backup Server

Utilize as informações nas tabelas seguintes para resolver problemas de erros que encontra ao utilizar o Azure Backup Server.

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Registar um cofre | Foram fornecidas credenciais de cofre inválidas. O ficheiro está danificado ou não ter as credenciais mais recentes associadas ao serviço de recuperação. | Ação recomendada: <br> <ul><li> Transferir o ficheiro de credenciais mais recentes a partir do cofre e tente novamente. <br>(OR)</li> <li> Se a ação anterior não funcionar, tente transferir as credenciais para um diretório local diferente ou crie um novo cofre. <br>(OR)</li> <li> Experimente atualizar a data e hora definições, conforme descrito em [este blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OR)</li> <li> Verifique se c:\windows\temp tem mais do que 65000 ficheiros. Mover ficheiros obsoletos para outra localização ou eliminar os itens na pasta Temp. <br>(OR)</li> <li> Verificar o estado de certificados. <br> a. Open **gerir certificados de computador** (no painel de controlo). <br> b. Expanda a **pessoais** nó e o respetivo nó filho **certificados**.<br> c.  Remover o certificado **ferramentas do Windows Azure**. <br> d. Repita o registo no cliente do Azure Backup. <br> (OR) </li> <li> Verifique se qualquer política de grupo está em vigor. </li></ul> |

## <a name="replica-is-inconsistent"></a>A réplica está inconsistente

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | A réplica está inconsistente | Certifique-se de que a opção de verificação de consistência automáticas no Assistente do grupo de proteção está ativada. Para obter mais informações sobre as causas de inconsistência de réplica e sugestões relevantes, consulte o artigo do Microsoft TechNet [a réplica está inconsistente](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> Em caso de cópia de segurança do Estado do sistema/BMR, certifique-se de que a cópia de segurança do Windows Server está instalada no servidor protegido.</li><li> Verifique a existência de problemas relacionados com o espaço no agrupamento de armazenamento do DPM no servidor de cópia de segurança do DPM/Microsoft Azure e atribuir armazenamento conforme necessário.</li><li> Verifique o estado do serviço de cópia de sombra de volumes no servidor protegido. Se estiver num estado desativado, defini-lo para iniciar manualmente. Inicie o serviço no servidor. Em seguida, voltar para a consola do DPM/Microsoft Azure Backup Server e iniciar a sincronização com verificação de consistência.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Falha na criação de ponto de recuperação online

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | Falha na criação de ponto de recuperação online | **Mensagem de erro**: Windows Azure Backup Agent não conseguiu criar um instantâneo do volume selecionado. <br> **Solução**: Tente aumentar o espaço no volume de pontos de recuperação e de réplica.<br> <br> **Mensagem de erro**: O agente de cópia de segurança do Windows Azure não é possível ligar ao serviço OBEngine <br> **Solução**: Certifique-se de que o OBEngine existe na lista de serviços em execução no computador. Se o serviço OBEngine não está em execução, utilize o comando "net start OBEngine" para iniciar o serviço OBEngine. <br> <br> **Mensagem de erro**: A frase de acesso de encriptação para este servidor não está definido. Configure uma frase de acesso de encriptação. <br> **Solução**: Tente configurar uma frase de acesso de encriptação. Se falhar, siga os passos seguintes: <br> <ol><li>Certifique-se de que existe a localização de rascunho. Esta é a localização que é mencionada no Registro **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, com o nome **ScratchLocation** devem existir.</li><li> Se existir a localização de rascunho, tente voltar a registar utilizando a frase de acesso antigo. *Sempre que configure uma frase de acesso de encriptação, guarde-o numa localização segura.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>As credenciais de cofre fornecidas são diferentes do cofre, que o servidor está registado

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Restauro | **Código de erro**: Erro de credenciais de cofre/CBPServerRegisteredVaultDontMatchWithCurrent: 100110 <br/> <br/>**Mensagem de erro**: As credenciais de cofre fornecidas são diferentes do cofre, que o servidor está registado | **Motivo**: Este problema ocorre quando estiver a tentar restaurar ficheiros para outro servidor do servidor original usando a opção de recuperação do DPM externo e, se o servidor que está a ser recuperado e o servidor original a partir de onde os dados são uma cópia de segurança não estão associados com o mesmo Cofre de serviço de recuperação.<br/> <br/>**Solução** para resolver este problema certifique-se tanto o servidor original e alternativo, está registado no mesmo cofre.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Falham de tarefas de criação de pontos de recuperação online para a VM de VMware

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | Tarefas de criação de pontos de recuperação online para a VM de VMware falharem. O DPM encontrou um erro do VMware ao tentar obter informações de ChangeTracking. Código de erro - FileFaultFault (ID 33621) |  <ol><li> Repor o CTK no VMware para VMs afetadas.</li> <li>Verifique esse disco independente não está em vigor no VMware.</li> <li>Parar a proteção de VMs afetadas e voltar a proteger com o **atualizar** botão. </li><li>Execute um CC para VMs afetadas.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>A operação do agente falhou devido a um erro de comunicação com o serviço de coordenador de agente do DPM no servidor

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Enviar os agentes para servidores protegidos | A operação de agente falhou devido a um erro de comunicação com o serviço Coordenador de agente do DPM em \<ServerName >. | **Se a ação recomendada mostrada no produto não funcionar, em seguida, execute os seguintes passos**: <ul><li> Se associar um computador a partir de um domínio não fidedigno, siga [essas etapas](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OR) </li><li> Se associar um computador a partir de um domínio fidedigno, resolver problemas com os passos descritos em [este blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OR)</li><li> Experimente a desativar o antivírus como um passo de resolução de problemas. Se isso resolve o problema, modifique as definições de antivírus como sugerido no [este artigo](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Configuração não conseguiu atualizar os metadados de registo

| Operação | Detalhes do erro | Solução |
|-----------|---------------|------------|
|Instalação | Configuração não conseguiu atualizar os metadados de registo. Esta falha de atualização pode provocar overusage do consumo de armazenamento. Para evitar isto, atualize a entrada de registo ReFS Trimming. | Ajustar a chave de registo **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Defina o valor Dword para 1. |
|Instalação | Configuração não conseguiu atualizar os metadados de registo. Esta falha de atualização pode provocar overusage do consumo de armazenamento. Para evitar esta situação, atualize a entrada do registo Volume SnapOptimization. | Criar a chave de registo **Manager\Configuration\VolSnapOptimization\WriteIds de proteção de dados de software\microsoft.** com um valor de cadeia de caracteres vazia. |

## <a name="registration-and-agent-related-issues"></a>Registo e problemas relacionados com o agente

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Enviar os agentes para servidores protegidos | As credenciais que são especificadas para o servidor são inválidas. | **Se a ação recomendada, que é mostrada no produto não funcionar, siga os passos seguintes**: <br> Tente instalar o agente de proteção manualmente no servidor de produção, conforme especificado nas [este artigo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| O agente de cópia de segurança do Azure não conseguiu ligar ao serviço de cópia de segurança do Azure (ID: 100050) | O Azure Backup Agent não conseguiu ligar ao serviço de cópia de segurança do Azure. | **Se a ação recomendada, que é mostrada no produto não funcionar, siga os passos seguintes**: <br>1. Execute o seguinte comando numa linha elevada: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Esta ação abre a janela do Internet Explorer. <br/> 2. Aceda a **ferramentas** > **opções da Internet** > **ligações** > **definições de LAN**. <br/> 3. Verifique as definições de proxy para a conta de sistema. Conjunto IP de Proxy e porta. <br/> 4. Feche o Internet Explorer.|
| Falha na instalação do agente de cópia de segurança do Azure | A instalação do Microsoft Azure Recovery Services falhou. Todas as alterações efetuadas no sistema pela instalação do Microsoft Azure Recovery Services foram revertidas. (ID: 4024) | Instale manualmente o agente do Azure.


## <a name="configuring-protection-group"></a>Configurar o grupo de proteção

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não conseguiu enumerar o componente da aplicação protegida no computador (nome do computador protegido). | Selecione **atualizar** no ecrã de interface do Usuário de grupo de proteção configurar ao nível da origem de dados/componente relevante. |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido é um SQL server, certifique-se de que as permissões de função de administrador do sistema foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido, tal como descrito em [este artigo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configurar grupos de proteção | Existe espaço livre suficiente no agrupamento de armazenamento para este grupo de proteção. | Os discos que são adicionados ao agrupamento de armazenamento [não deve conter uma partição](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Elimine quaisquer volumes existentes nos discos. Em seguida, adicioná-los ao agrupamento de armazenamento.|
| Alteração de política |Não foi possível modificar a política de cópia de segurança. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo ter passado. Se o problema persistir, contacte o suporte da Microsoft. | **Causa:**<br/>Este erro ocorre em três condições: quando as definições de segurança estiverem ativadas, quando tenta reduzir o período de retenção abaixo os valores mínimo especificado anteriormente, e quando estiver a utilizar uma versão não suportada. (Não suportadas de versões são aqueles abaixo versão 2.0.9052 do Microsoft Azure Backup Server e a atualização 1 do servidor de cópia de segurança do Azure.) <br/>**Ação recomendada:**<br/> Para continuar com as atualizações relacionadas à política, defina o período de retenção acima o período especificado de retenção mínimo. (O período de retenção mínimo é de sete dias para diariamente, quatro semanas para semanais, três semanas para mensalmente ou um ano para anualmente.) <br><br>Opcionalmente, outro preferencial abordagem é atualizar o agente de cópia de segurança e o servidor de cópia de segurança do Azure para tirar partido de todas as atualizações de segurança. |

## <a name="backup"></a>Cópia de segurança

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança | Erro inesperado durante a execução da tarefa. O dispositivo não está pronto. | **Se a ação recomendada, que é mostrada no produto não funcionar, siga os passos seguintes:** <br> <ul><li>Defina o espaço de armazenamento de cópia de sombra para ilimitado nos itens do grupo de proteção e, em seguida, execute a verificação de consistência.<br></li> (OR) <li>Tente eliminar a proteção existente grupo e a criação de vários grupos de novo. Cada novo grupo de proteção deve conter um item individual.</li></ul> |
| Cópia de segurança | Se estiver fazendo o backup apenas o estado do sistema, certifique-se de que existe espaço livre suficiente no computador protegido para armazenar a cópia de segurança do Estado do sistema. | <ol><li>Certifique-se de que a cópia de segurança do Windows Server está instalada na máquina protegida.</li><li>Certifique-se de que existe espaço suficiente no computador protegido para o estado do sistema. A maneira mais fácil para verificar isso é para ir para o computador protegido, abra a cópia de segurança do Windows Server, clique nas seleções e, em seguida, selecionar a BMR. A interface do Usuário, em seguida, indica quanto espaço é necessário. Open **WSB** > **Local backup** > **agenda de cópia de segurança** > **selecionar configuração de cópia de segurança**  >  **Completo do servidor** (tamanho é exibido). Utilize este tamanho para a verificação.</li></ol>
| Cópia de segurança | Falha de cópia de segurança de BMR | Se o tamanho de BMR for grande, mover alguns ficheiros de aplicação para a unidade do SO e tente novamente. |
| Cópia de segurança | Não mostrar como disponível para adicionar a opção para voltar a proteger uma VM do VMware num novo servidor de cópia de segurança do Azure de Microsoft. | Propriedades do VMware são apontaram para uma instância antiga, extinto do Microsoft Azure Backup Server. Para resolver este problema:<br><ol><li>No VCenter (equivalente do SC VMM), vá para o **resumo** separador e clique em **Vlastní Atributy**.</li>  <li>Eliminar o nome antigo do Microsoft Azure Backup Server partir da **DPMServer** valor.</li>  <li>Volte para o novo servidor de cópia de segurança do Azure do Microsoft e modificar o PG.  Depois de selecionar o **atualizar** botão, a VM é apresentada uma caixa de verificação como estando disponíveis para adicionar a proteção.</li></ol> |
| Cópia de segurança | Erro ao aceder a pastas/ficheiros partilhados | Experimente modificar as definições de antivírus como sugerido no artigo da TechNet [executar software antivírus no servidor do DPM](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Frase de acesso de alteração

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Frase de acesso de alteração |A segurança do PIN que introduziu está incorreta. Fornece o PIN para concluir esta operação de segurança correta. |**Causa:**<br/> Este erro ocorre quando insere um inválido ou expirado PIN de segurança enquanto estiver a efetuar uma operação crítica (como alterar uma frase de acesso). <br/>**Ação recomendada:**<br/> Para concluir a operação, tem de introduzir um PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e vá para o Cofre dos serviços de recuperação. Em seguida, aceda a **configurações** > **propriedades** > **gerar PIN de segurança**. Utilize este PIN para alterar a frase de acesso. |
| Frase de acesso de alteração |Falha na operação. ID: 120002 |**Causa:**<br/>Este erro ocorre quando as definições de segurança estiverem ativadas, ou quando tentar alterar a frase de acesso quando estiver a utilizar uma versão não suportada.<br/>**Ação recomendada:**<br/> Para alterar a frase de acesso, primeiro tem de atualizar o agente de cópia de segurança para a versão mínima, que é 2.0.9052. Terá também de atualizar o servidor de cópia de segurança do Azure para o requisito mínimo: atualização 1 e, em seguida, introduza um PIN de segurança válido. Para obter o PIN, inicie sessão no portal do Azure e vá para o Cofre dos serviços de recuperação. Em seguida, aceda a **configurações** > **propriedades** > **gerar PIN de segurança**. Utilize este PIN para alterar a frase de acesso. |


## <a name="configure-email-notifications"></a>Configurar notificações por e-mail

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Como configurar notificações por e-mail com uma conta do Office 365 |ID do erro: 2013| **Causa:**<br> A tentar utilizar a conta do Office 365 <br>**Ação recomendada:**<ol><li> A primeira coisa para garantir que é o que "Permitir anónimo reencaminhamento num conector de recebimento" para o seu servidor do DPM está configurado no Exchange. Para obter mais informações sobre como configurar esta opção, consulte [permitir reencaminhamento anónimo em conector de recebimento](https://technet.microsoft.com/library/bb232021.aspx) no TechNet.</li> <li> Se não pode utilizar um reencaminhamento de SMTP interno e tem de configurar com o seu servidor do Office 365, pode configurar o IIS para ser um reencaminhamento. Configurar o servidor do DPM para [reencaminhamento de SMTP, ao O365, usando o IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **IMPORTANTE:** Certifique-se de que o utilizador\@formato de domínio. com e *não* domínio \ utilizador.<br><br><li>Ponto de DPM para utilizar o nome do servidor local como servidor de SMTP, a porta 587. Em seguida, aponte para o e-mail do utilizador que devem ser proveniente de e-mails.<li> O nome de utilizador e palavra-passe na página de configuração do DPM SMTP devem ser para uma conta de domínio no domínio que se encontra no DPM. </li><br> **NOTA**: Quando está a alterar o endereço do servidor SMTP, faça a alteração para as novas definições, feche a caixa de definições e, em seguida, volte a abri-lo para Certifique-se de que reflitam o novo valor.  Basta alterar e teste podem não sempre fazer com que as novas definições surtam efeito, para que testá-lo dessa maneira é uma prática recomendada.<br><br>Em qualquer altura durante este processo, pode limpar estas definições ao fechar a consola do DPM e editar as seguintes chaves do registo: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> chaves eliminar SMTPPassword e SMTPUserName**. Pode adicioná-los novamente para a interface do Usuário quando iniciá-lo novamente.
