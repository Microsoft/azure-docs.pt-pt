---
title: Resolução de problemas do Azure Backup Server
description: Instalação de resolução de problemas, registo do Azure Backup Server e cópia de segurança e restauro das cargas de trabalho da aplicação.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 09e5fe5da7e316257cbbdcb89074fe8a4bc692c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403012"
---
# <a name="troubleshoot-azure-backup-server"></a>Resolução de problemas do Azure Backup Server

Utilize as informações nas seguintes tabelas para resolver os erros que encontra durante a utilização do Servidor de Backup Azure.

## <a name="basic-troubleshooting"></a>Resolução de problemas básicos

Recomendamos que efetue a seguinte validação, antes de começar a resolver problemas no Microsoft Azure Backup Server (MABS):

- [Certifique-se de que o Agente dos Serviços de Recuperação do Microsoft Azure (MARS) está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Certifique-se de que há conectividade de rede entre o agente MARS e o Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Certifique-se de que os Serviços de Recuperação do Microsoft Azure estão em execução (na consola do Serviço). Se necessário, reinicie e relemque a operação
- [Certifique-se de que 5 a 10% de espaço livre do volume está disponível na localização da pasta de rascunho](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- Se o registo estiver a falhar, certifique-se de que o servidor em que está a tentar instalar o Azure Backup Server ainda não está registado noutro cofre.
- Se a instalação Push falhar, verifique se o agente DPM já está instalado. Caso esteja, desinstale o agente e volte a tentar a instalação
- [Confirme que nenhum outro processo ou software antivírus está a interferir com o Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Certifique-se de que o serviço sql Agent está em execução e definido para automático no servidor MABS<br>

## <a name="configure-antivirus-for-mabs-server"></a>Configure o antivírus para o servidor MABS

O MABS é compatível com os produtos de software antivírus mais populares. Recomendamos as seguintes medidas para evitar conflitos:

1. **Desativar a monitorização em tempo real** - desative a monitorização em tempo real pelo software antivírus para o seguinte:
    - `C:\Program Files<MABS Installation path>\XSD` pasta
    - `C:\Program Files<MABS Installation path>\Temp` pasta
    - Carta de unidade do volume moderno de armazenamento de backup
    - Registos de réplica e transferência: Para o fazer, desative a monitorização em tempo real de **dpmra.exe**, que se encontra na pasta `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . A monitorização em tempo real degrada o desempenho porque o software antivírus verifica as réplicas cada vez que o MABS sincroniza com o servidor protegido, e digitaliza todos os ficheiros afetados sempre que o MABS aplica alterações às réplicas.
    - Consola de administrador: Para evitar um impacto no desempenho, desative a monitorização em tempo real do processo **decsc.exe.** O processo **csc.exe** é o \# compilador C, e a monitorização em tempo real pode degradar o desempenho porque o software antivírus verifica ficheiros que o processo **csc.exe** emite quando gera mensagens XML. **CSC.exe** situa-se nos seguintes caminhos:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - Para o agente MARS instalado no servidor MABS, recomendamos que exclua os seguintes ficheiros e localizações:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` como um processo
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Localização de risco (se não estiver a utilizar a localização padrão)
2. **Desativar a monitorização**em tempo real no servidor protegido : Desative a monitorização em tempo real de **dpmra.exe**, que se encontra na pasta `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` , no servidor protegido.
3. **Configure o software antivírus para eliminar os ficheiros infetados em servidores protegidos e no servidor MABS**: Para evitar a corrupção de dados de réplicas e pontos de recuperação, configuure o software antivírus para eliminar ficheiros infetados, em vez de os limpar ou em quarentena automaticamente. A limpeza automática e a quarentena podem fazer com que o software antivírus modifique ficheiros, fazendo alterações que o MABS não consegue detetar.

Deve executar uma sincronização manual com uma consistência. Verifique o trabalho sempre que o software antivírus elimina um ficheiro da réplica, mesmo que a réplica seja marcada como inconsistente.

### <a name="mabs-installation-folders"></a>Pastas de instalação MABS

As pastas de instalação predefinidos para DPM são as seguintes:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Também pode executar o seguinte comando para encontrar o caminho da pasta de instalação:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Credenciais de cofre inválidas fornecidas

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Registrando-se em um cofre | Foram fornecidas credenciais de cofre inválidas. O ficheiro é corrompido ou não tem as mais recentes credenciais associadas ao serviço de recuperação. | Ação recomendada: <br> <ul><li> Descarregue o ficheiro de credenciais mais recentes do cofre e tente de novo. <br>(OU)</li> <li> Se a ação anterior não funcionou, tente transferir as credenciais para um diretório local diferente ou crie um novo cofre. <br>(OU)</li> <li> Tente atualizar as definições de data e hora conforme descrito [neste artigo](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided). <br>(OU)</li> <li> Verifique se c:\windows\temperatura tem mais de 65000 ficheiros. Mover ficheiros velhos para outro local ou eliminar os itens na pasta Temp. <br>(OU)</li> <li> Verifique o estado dos certificados. <br> a. Abrir **Certificados de Computador** (no Painel de Controlo). <br> b. Expandir o **nó pessoal** e os seus **certificados de**nó para crianças.<br> c.  Remova o certificado **Ferramentas Azure do Windows**. <br> d. Re-preenquanto o registo no cliente Azure Backup. <br> (OU) </li> <li> Verifique se existe alguma política de grupo. </li></ul> |

## <a name="replica-is-inconsistent"></a>A réplica é inconsistente

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | A réplica é inconsistente | Verifique se a opção de verificação de consistência automática no Assistente do Grupo de Proteção está ligada. Para obter mais informações sobre opções de replicação e verificações de consistência, consulte [este artigo.](/system-center/dpm/create-dpm-protection-groups)<br> <ol><li> No caso de cópia de segurança Do Estado do Sistema/BMR, verifique se o Windows Server Backup está instalado no servidor protegido.</li><li> Verifique se há problemas relacionados com o espaço no conjunto de armazenamento DPM no DPM/Microsoft Azure Backup Server e aloque o armazenamento conforme necessário.</li><li> Verifique o estado do Serviço de Cópia sombra de volume no servidor protegido. Se estiver em estado de desativação, desative-o para começar manualmente. Inicie o serviço no servidor. Em seguida, volte para a consola DPM/Microsoft Azure Backup Server e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Falha na criação de ponto de recuperação online

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | Falha na criação de ponto de recuperação online | **Error Message**: O Windows Azure Backup Agent não foi capaz de criar uma imagem do volume selecionado. <br> **Solução alternativa**: Tente aumentar o espaço no volume de réplica e ponto de recuperação.<br> <br> **Error Message**: O Windows Azure Backup Agent não pode ligar-se ao serviço OBEngine <br> **Solução:** verifique se o OBEngine existe na lista de serviços de execução no computador. Se o serviço OBEngine não estiver em funcionamento, utilize o comando "net start OBEngine" para iniciar o serviço OBEngine. <br> <br> **Error Message**: A palavra-passe de encriptação para este servidor não está definida. Por favor, configuure uma palavra-passe de encriptação. <br> **Solução alternativa**: Tente configurar uma palavra-passe de encriptação. Se falhar, tome os seguintes passos: <br> <ol><li>Verifique se a localização do risco existe. Esta é a localização que é mencionada no ** registoHKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config, **com o nome **ScratchLocation** deve existir.</li><li> Se a localização do risco existir, tente voltar a registar-se utilizando a frase de passagem antiga. *Sempre que configurar uma palavra-passe de encriptação, guarde-a num local seguro.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Os servidores DPM originais e externos devem estar registados no mesmo cofre

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Restauro | **Código de erro**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Error message**: Os servidores DPM originais e externos devem estar registados no mesmo cofre | **Causa**: Este problema ocorre quando está a tentar restaurar ficheiros para o servidor alternativo a partir do servidor original utilizando a opção de recuperação de DPM Externo, e se o servidor que está a ser recuperado e o servidor original de onde os dados são apoiados não estão associados ao mesmo cofre dos Serviços de Recuperação.<br/> <br/>**Solução alternativa** Para resolver este problema, certifique-se de que tanto o servidor original como o servidor alternativo estão registados no mesmo cofre.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Empregos de criação de ponto de recuperação online para VMware VM falha

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | Os trabalhos de criação de pontos de recuperação online para vMware VM falham. O DPM encontrou um erro da VMware enquanto tentava obter informações do ChangeTracking. ErrorCode - FileFaultFault (ID 33621) |  <ol><li> Repor o CTK em VMware para os VMs afetados.</li> <li>Verifique se o disco independente não está no lugar no VMware.</li> <li>Pare a proteção para os VMs afetados e reproteja com o botão **Refresh.** </li><li>Executar um CC para os VMs afetados.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>A operação do agente falhou devido a um erro de comunicação com o serviço de coordenador de agente DPM no servidor

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Agentes de pressão para servidores protegidos | A operação do agente falhou devido a um erro de comunicação com o serviço de Coordenador do Agente DPM em \<ServerName> . | **Se a ação recomendada mostrada no produto não funcionar, então execute os seguintes passos:** <ul><li> Se estiver a ligar um computador a partir de um domínio não fidedigno, siga [estes passos](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains). <br> (OU) </li><li> Se estiver a anexar um computador a partir de um domínio fidedigno, resolva os problemas utilizando os passos descritos [neste blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>(OU)</li><li> Tente desativar o antivírus como um passo de resolução de problemas. Se resolver o problema, modifique as definições antivírus como sugerido neste [artigo](/system-center/dpm/run-antivirus-server).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A configuração não conseguiu atualizar os metadados de registo

| Operação | Detalhes do erro | Solução |
|-----------|---------------|------------|
|Instalação | A configuração não conseguiu atualizar metadados de registo. Esta falha de atualização pode levar a um consumo excessivo de armazenamento. Para evitar esta atualização, a entrada do registo de corte reFS. | Ajuste a chave de registo **SYSTEM\CurrentControlSet\Control\Control\FileSystem\RefsEnableInlineTrim**. Desa estarda o valor Dword para 1. |
|Instalação | A configuração não conseguiu atualizar metadados de registo. Esta falha de atualização pode levar a um consumo excessivo de armazenamento. Para evitar isto, atualize a entrada do registo de SnapOptimization volume. | Criar a chave de registo **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** com um valor de cadeia vazio. |

## <a name="registration-and-agent-related-issues"></a>Questões relacionadas com o registo e o agente

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Agentes de pressão para servidores protegidos | As credenciais especificadas para o servidor são inválidas. | **Se a ação recomendada que é mostrada no produto não funcionar, tome os seguintes passos:** <br> Tente instalar o agente de proteção manualmente no servidor de produção, conforme especificado [neste artigo](/system-center/dpm/deploy-dpm-protection-agent).|
| O Agente de Backup Azure não conseguiu ligar-se ao serviço de backup Azure (ID: 100050) | O Agente de Reserva Azure não conseguiu ligar-se ao serviço de backup da Azure. | **Se a ação recomendada que é mostrada no produto não funcionar, tome os seguintes passos:** <br>1. Executar o seguinte comando a partir de um pedido elevado: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Isto abre a janela do Internet Explorer. <br/> 2. Aceda às **Tools**  >  **opções de internet**  >  **ferramentas Configurações**  >  **DE LIGAções LAN**. <br/> 3. Altere as definições para utilizar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<br/> 4. Se a sua máquina tiver acesso limitado à Internet, certifique-se de que as definições de firewall na máquina ou procuração permitem estes [URLs](install-mars-agent.md#verify-internet-access) e [endereço IP](install-mars-agent.md#verify-internet-access).|
| A instalação do Agente de Backup Azure falhou | A instalação dos Serviços de Recuperação do Microsoft Azure falhou. Todas as alterações que foram feitas ao sistema pela instalação dos Serviços de Recuperação do Microsoft Azure foram revertidas. (ID: 4024) | Instale manualmente o Agente Azure.

## <a name="configuring-protection-group"></a>Grupo de proteção de configuração

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não conseguiu enumerar o componente de aplicação no computador protegido (nome de computador protegido). | Selecione **Refresh** no ecrã UI do grupo de proteção de configuração no nível de fonte/componente relevante. |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido for um servidor SQL, verifique se as permissões de função sysadmin foram fornecidas à conta do sistema (NTAuthority\System) no computador protegido, conforme descrito neste [artigo.](/system-center/dpm/back-up-sql-server)
| Configurar grupos de proteção | Não há espaço livre suficiente na piscina de armazenamento para este grupo de proteção. | Os discos adicionados à piscina de armazenamento [não devem conter uma divisória](/system-center/dpm/create-dpm-protection-groups). Elimine os volumes existentes nos discos. Em seguida, adicione-os à piscina de armazenamento.|
| Mudança de política |A política de apoio não pôde ser modificada. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Por favor, recaia a operação depois de algum tempo passado. Se o problema persistir, contacte o suporte da Microsoft. | **Causa:**<br/>Este erro ocorre em três condições: quando as definições de segurança estão ativadas, quando se tenta reduzir o intervalo de retenção abaixo dos valores mínimos especificados anteriormente e quando se está numa versão não suportada. (As versões não suportadas são as mais baixas do que a versão 2.0.9052 do Microsoft Azure Backup Server e a atualização do Servidor de Backup 1.) <br/>**Ação recomendada:**<br/> Para prosseguir com as atualizações relacionadas com a política, detenda o período de retenção acima do período mínimo de retenção especificado. (O período mínimo de retenção é de sete dias para cada dia, quatro semanas para semanalmente, três semanas para mensal ou um ano para o ano.) <br><br>Opcionalmente, outra abordagem preferida é atualizar o agente de backup e o Azure Backup Server para alavancar todas as atualizações de segurança. |

## <a name="backup"></a>Backup

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Backup | Ocorreu um erro inesperado enquanto o trabalho estava a decorrer. O dispositivo não está pronto. | **Se a ação recomendada que é mostrada no produto não funcionar, tome os seguintes passos:** <br> <ul><li>Desabrar o espaço de armazenamento de cópias de sombra para ilimitados nos itens do grupo de proteção e, em seguida, executar a verificação de consistência.<br></li> (OU) <li>Tente eliminar o grupo de proteção existente e criar vários novos grupos. Cada novo grupo de proteção deve ter um item individual nele.</li></ul> |
| Backup | Se estiver a fazer apenas o backup do sistema, verifique se existe espaço livre suficiente no computador protegido para armazenar a cópia de segurança do estado do sistema. | <ol><li>Verifique se a cópia de segurança do Servidor do Windows está instalada na máquina protegida.</li><li>Verifique se há espaço suficiente no computador protegido para o estado do sistema. A maneira mais fácil de verificar isto é ir para o computador protegido, abrir o Windows Server Backup, clicar nas seleções e, em seguida, selecionar BMR. A UI então lhe diz quanto espaço é necessário. Abra o horário de backup local **WSB**  >  **Local backup**  >  **Backup schedule**  >  **Select Backup Configuration**Full  >  **server** (o tamanho é apresentado). Use este tamanho para verificação.</li></ol>
| Backup | Falha no back up para bMR | Se o tamanho do BMR for grande, mova alguns ficheiros de aplicação para a unidade de so e redaça. |
| Backup | A opção de reprotegir um VMware VM num novo Microsoft Azure Backup Server não mostra como disponível para adicionar. | As propriedades VMware são apontadas para um caso antigo e aposentado do Microsoft Azure Backup Server. Para resolver este problema:<br><ol><li>No VCenter (equivalente SC-VMM), aceda ao **separador Resumo** e, em seguida, aos **Atributos Personalizados**.</li>  <li>Elimine o antigo nome do Servidor de Backup do Microsoft Azure do valor **DPMServer.**</li>  <li>Volte para o novo Microsoft Azure Backup Server e modifique o PG.  Depois de selecionar o botão **Refresh,** o VM aparece com uma caixa de verificação como disponível para adicionar à proteção.</li></ol> |
| Backup | Erro ao aceder a ficheiros/pastas partilhadas | Tente modificar as definições antivírus como sugerido neste artigo [Executar o software antivírus no servidor DPM](/system-center/dpm/run-antivirus-server).|

## <a name="change-passphrase"></a>Alterar a frase de passe

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Alterar a frase de passe |O PIN de segurança que foi introduzido está incorreto. Forneça o PIN de segurança correto para completar esta operação. |**Causa:**<br/> Este erro ocorre quando introduz um PIN de segurança inválido ou expirado durante uma operação crítica (como alterar uma palavra-passe). <br/>**Ação recomendada:**<br/> Para completar a operação, tem de introduzir um PIN de segurança válido. Para obter o PIN, inscreva-se no portal Azure e vá ao cofre dos Serviços de Recuperação. Em seguida, vá a **Definições**  >  **Propriedades**  >  **Gerar PIN de Segurança**. Utilize este PIN para alterar a palavra-passe. |
| Alterar a frase de passe |A operação falhou. ID: 120002 |**Causa:**<br/>Este erro ocorre quando as definições de segurança estão ativadas, ou quando tenta alterar a palavra-passe quando está a utilizar uma versão não suportada.<br/>**Ação recomendada:**<br/> Para alterar a palavra-passe, tem primeiro de atualizar o agente de backup para a versão mínima, que é 2.0.9052. Também precisa de atualizar o Azure Backup Server ao mínimo de atualização 1 e, em seguida, introduzir um PIN de segurança válido. Para obter o PIN, inscreva-se no portal Azure e vá ao cofre dos Serviços de Recuperação. Em seguida, vá a **Definições**  >  **Propriedades**  >  **Gerar PIN de Segurança**. Utilize este PIN para alterar a palavra-passe. |

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Criação de notificações de e-mail usando uma conta de trabalho ou escola |ID de erro: 2013| **Causa:**<br> Tentar usar o trabalho ou a conta escolar <br>**Ação recomendada:**<ol><li> A primeira coisa a garantir é que "Permitir o Retransmissor Anónimo num Conector De Receção" para o seu servidor DPM está configurado no Exchange. Para obter mais informações sobre como configurar isto, consulte [Permitir o Retransmissor Anónimo num Conector De Receção](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Se não puder utilizar um relé SMTP interno e precisar de configurar utilizando o servidor Office 365, pode configurar o IIS para ser um retransmissor. Configure o servidor DPM para [transmitir o SMTP ao Office 365 utilizando o IIS](/exchange/mail-flow/test-smtp-with-telnet).<br><br>  Certifique-se de que utiliza o formato domain.com utilizador \@ e *não* o domínio\utilizador.<br><br><li>Ponto DPM para usar o nome do servidor local como servidor SMTP, porta 587. Em seguida, aponte para o e-mail do utilizador de que os e-mails devem vir.<li> O nome de utilizador e a palavra-passe na página de configuração do DPM SMTP devem ser para uma conta de domínio no domínio em que o DPM está ligado. </li><br> Quando estiver a alterar o endereço do servidor SMTP, faça a alteração para as novas definições, feche a caixa de definições e, em seguida, reabram-na para se certificar de que reflete o novo valor.  Simplesmente mudar e testar pode nem sempre fazer com que as novas definições produzam efeito, por isso testá-lo desta forma é uma boa prática.<br><br>A qualquer momento durante este processo, pode limpar estas definições fechando a consola DPM e editando as seguintes teclas de registo: **HKLM\SOFTWARE\Microsoft\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName keys**. Pode adicioná-los de volta à UI quando o lançar novamente.

## <a name="common-issues"></a>Problemas comuns

Esta secção cobre os erros comuns que pode encontrar durante a utilização do Servidor de Backup Azure.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Mensagem de erro | Ação recomendada |
-- | --
A cópia de segurança falhou porque a réplica da cópia de segurança do disco é inválida ou está em falta. | Para resolver este problema, verifique os passos abaixo e relemisse a operação: <br/> 1. Criar um ponto de recuperação do disco<br/> 2. Verifique a consistência da fonte de dados <br/> 3. Parar a proteção da fonte de dados e, em seguida, reconfigurar a proteção para esta fonte de dados

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo de volume de origem falhou porque os metadados na réplica são inválidos. | Crie um ponto de recuperação de disco desta fonte de dados e volte a tentar a cópia de segurança online

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Mensagem de erro | Ação recomendada |
-- | --
O instantâneo de volume de origem falhou devido a uma réplica inconsistente de fontes de dados. | Faça uma verificação de consistência nesta fonte de dados e tente novamente

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Mensagem de erro | Ação recomendada |
-- | --
A cópia de segurança falhou porque não foi possível clonar a réplica da cópia de segurança do disco.| Certifique-se de que todos os ficheiros de réplicas de cópias de cópias de segurança de discos anteriores (.vhdx) estão desmontados e que não está em curso nenhum disco para a cópia de segurança do disco durante as cópias de segurança online
