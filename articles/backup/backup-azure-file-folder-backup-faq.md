---
title: Agente de cópia de segurança do Azure FAQ
description: Respostas a perguntas comuns sobre como funciona o agente do Azure Backup e os limites das cópias de segurança e de retenção.
services: backup
author: trinadhk
manager: shreeshd
keywords: recuperação de cópia de segurança e após desastres; serviço de cópia de segurança
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: trinadhk
ms.openlocfilehash: acf71ae6f37ab6ea32d9cdd0ac06f297b00fba2e
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918574"
---
# <a name="questions-about-the-azure-backup-agent"></a>Perguntas sobre o agente do Azure Backup
Este artigo tem respostas a perguntas comuns para o ajudar a compreender rapidamente os componentes do agente do Azure Backup. Em algumas das respostas, existem ligações para os artigos que incluem informação abrangente. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurar a cópia de segurança
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Onde posso transferir o agente do Backup do Azure mais recente? <br/>
Pode transferir o agente mais recente para fazer a cópia de segurança do Windows Server, do System Center DPM ou do cliente Windows [aqui](https://aka.ms/azurebackup_agent). Se pretender efetuar a cópia de segurança de uma máquina virtual, utilize o Agente da VM (que instala automaticamente a extensão correta). O Agente da VM já está presente nas máquinas virtuais criadas a partir da galeria do Azure.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Quando configuro o agente do Azure Backup, é-me pedido que introduza as credenciais do cofre. As credenciais do cofre expiram?
Sim, as credenciais do cofre expiram após 48 horas. Se o ficheiro expirar, inicie sessão no portal do Azure e transfira os ficheiros de credenciais do cofre a partir do seu cofre.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>A partir de que tipos de unidades posso criar cópias de segurança de ficheiros e pastas? <br/>
Não consegue realizar uma cópia de segurança das seguintes unidades/volumes:

* Suporte de dados amovível: Todas as origens de item de cópia de segurança tem de ser comunicadas como fixas.
* Volumes de só de leitura: O volume tem de ser gravável para o serviço de cópia de sombra de volumes (VSS) para funcionar.
* Offline Volumes: O volume tem de estar online para que o VSS funcione.
* Partilha de rede: O volume tem de ser local no servidor para ser feita com a cópia de segurança online.
* Volumes protegidos de BitLocker: O volume tem de ser desbloqueado antes da cópia de segurança ocorrer.
* Identificação do sistema de ficheiros: O NTFS é o único sistema de ficheiros suportado.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>De que tipos de ficheiros e pastas posso criar cópias de segurança a partir do meu servidor?<br/>
São suportados os seguintes tipos:

* Encriptados
* Comprimidos
* Dispersos
* Comprimidos + dispersos
* Hiperligações: Não suportado, ignorado
* Ponto de reanálise: Não suportado, ignorado
* Encriptados + dispersos: Não suportado, ignorado
* Stream comprimido: Não suportado, ignorado
* Stream disperso: Não suportado, ignorado

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Pode instalar o agente do Backup do Azure numa VM do Azure cuja cópia de segurança já foi efetuada pelo serviço de Backup do Azure através da extensão da VM? <br/>
Com certeza. O Backup do Azure fornece uma cópia de segurança ao nível da VM para VMs do Azure com a extensão da VM. Para proteger ficheiros e pastas no SO Windows convidado, instale o agente do Azure Backup no SO Windows convidado.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Posso instalar o agente do Backup do Azure numa VM do Azure para efetuar uma cópia de segurança de ficheiros e pastas presentes no armazenamento temporário fornecido pela VM do Azure? <br/>
Sim. Instale o agente do Azure Backup no SO Windows convidado e crie uma cópia de segurança de ficheiros e pastas no armazenamento temporário. As tarefas de cópia de segurança falham depois de os dados de armazenamento temporário serem eliminados. Além disso, se os dados de armazenamento temporário tiverem sido eliminados, só pode restaurar para o armazenamento não volátil.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Qual é o requisito de tamanho mínimo para a pasta da cache? <br/>
O tamanho da pasta cache determina a quantidade de dados para a cópia de segurança. O volume de sua pasta de cache deve ser, pelo menos, 5 a 10% livre espaço, em comparação com o tamanho total dos dados de cópia de segurança. Se o volume tiver menos de 5% de espaço livre, a aumentar o tamanho do volume, ou [mover a pasta de cache para um volume com espaço livre suficiente](backup-azure-file-folder-backup-faq.md#backup).

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Como registar o meu servidor para outro datacenter?<br/>
Os dados de cópia de segurança são enviados para o datacenter do cofre no qual está registado. A forma mais fácil de alterar o datacenter é desinstalar o agente e reinstalá-lo e registá-lo num cofre novo que pertença ao datacenter pretendido.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>O agente do Azure Backup funciona num servidor que utiliza a eliminação de duplicados do Windows Server 2012? <br/>
Sim. O serviço do agente converte os dados com duplicados eliminados em dados normais quando prepara a operação de cópia de segurança. Em seguida, otimiza os dados para a cópia de segurança, encripta os dados e, em seguida, envia os dados encriptados para o serviço de cópia de segurança online.

## <a name="prerequisites-and-dependencies"></a>Pré-requisitos e dependências
### <a name="what-features-of-microsoft-azure-recovery-services-mars-agent-require-net-framework-452-and-higher"></a>Que funcionalidades do agente dos serviços de recuperação do Azure (MARS) da Microsoft exigem o .NET framework 4.5.2 e superior?
O [restaurar instantânea](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) funcionalidade que permite a restauração de arquivos individuais e pastas a partir *recuperar dados* assistente requer o .NET Framework 4.5.2 ou superior.

## <a name="backup"></a>Cópia de segurança
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Como posso alterar a localização da cache especificada para o agente do Backup do Azure?<br/>
Utilize a lista seguinte para alterar a localização da cache.

1. Pare o motor de Cópia de Segurança executando o seguinte comando numa linha de comandos elevada:

    ```PS C:\> Net stop obengine``` 
  
2. Não mova os ficheiros. Em vez disso, copie a pasta do espaço na cache para outra unidade com espaço suficiente. O espaço da cache original pode ser removido após confirmar que as cópias de segurança estão a trabalhar com o novo espaço da cache.
3. Atualize as seguintes entradas de registo com o caminho para a nova pasta de espaço da cache.<br/>

    | Caminho do registo | Chave do Registo | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta cache* |

4. Reinicie o motor do Backup com o comando seguinte numa linha de comandos elevada:

    ```PS C:\> Net start obengine```

Assim que a criação da cópia de segurança tiver sido concluída com êxito na nova localização da cache, pode remover a pasta cache original.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Onde posso colocar a pasta da cache do Agente do Azure Backup para funcionar como esperado?<br/>
As seguintes localizações para a pasta da cache não são recomendadas:

* Partilha de rede ou mídia removível: A pasta da cache tem de ser local no servidor que necessita de cópia de segurança utilizando a cópia de segurança online. Localizações de rede ou mídia removível como unidades USB não é suportada
* Offline Volumes: A pasta da cache tem de estar online para a cópia de segurança esperada com o agente de cópia de segurança do Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Existem atributos da pasta cache que não são suportados?<br/>
Os atributos ou respetivas combinações seguintes não são suportadas para a pasta de cache:

* Encriptados
* Duplicados eliminados
* Comprimidos
* Dispersos
* Ponto de Reanálise

A pasta de cache e o VHD de metadados não têm os atributos necessários para o agente do Azure Backup.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Existe alguma forma de ajustar a quantidade de largura de banda utilizada pelo serviço de Cópia de Segurança?<br/>
  Sim, utilize a opção **Alterar Propriedades** no Agente do Backup para ajustar a largura de banda. Pode ajustar a quantidade de largura de banda e as horas em que a utiliza. Para obter instruções passo a passo, veja **[Ativar a limitação de rede](backup-configure-vault.md#enable-network-throttling)**.

## <a name="manage-backups"></a>Gerir cópias de segurança
### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>O que acontece se mudar o nome de um servidor do Windows que está a efetuar a cópia de segurança dos dados para o Azure?<br/>
Quando mudar o nome de um servidor, todas as cópias de segurança atualmente configuradas são paradas. Registe o nome novo do servidor no cofre do Backup. Quando registar o nome novo no cofre, a primeira operação de cópia de segurança é uma cópia de segurança *completa*. Se precisar de recuperar dados de cópias de segurança no cofre com o nome do servidor antigo, utilize a opção [**Outro servidor**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) no assistente **Recuperar Dados**.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Qual é o comprimento do caminho do ficheiro máximo que pode ser especificado na política do Backup com o agente do Azure Backup? <br/>
O agente do Backup do Azure depende do NTFS. A [especificação do comprimento do caminho do ficheiro está limitada pela API do Windows](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths). Se os ficheiros que pretende proteger têm um comprimento de caminho de ficheiro maior do que o permitido pela API do Windows, crie uma cópia de segurança da pasta principal ou da unidade de disco.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Que carateres são permitidos no caminho de ficheiro da política do Backupdo Azure com o agente do Backup do Azure? <br>
 O agente do Backup do Azure depende do NTFS. Permite a [carateres suportados pelo NTFS](/windows/desktop/FileIO/naming-a-file#naming_conventions) como parte da especificação do ficheiro. 
 
### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Recebo o aviso "As Cópias de Segurança do Azure não foram configuradas para este servidor", embora tenha configurado uma política de cópias de segurança <br/>
Este aviso ocorre quando as definições do agendamento de cópias de segurança armazenadas no servidor local não são as mesmas que as definições armazenadas no cofre de cópias de segurança. Quando o servidor ou as definições tenham sido recuperadas para um bom estado conhecido, as agendas de cópia de segurança podem perder a sincronização. Se receber este aviso, [reconfigure a política de cópia de segurança](backup-azure-manage-windows-server.md) e, em seguida, **Execute a Cópia de Segurança Agora** para ressincronizar o servidor local com o Azure.
