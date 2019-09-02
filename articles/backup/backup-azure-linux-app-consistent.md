---
title: 'Backup do Azure: backups consistentes com o aplicativo de VMs Linux'
description: Crie backups consistentes com o aplicativo de suas máquinas virtuais Linux para o Azure. Este artigo explica como configurar a estrutura de script para fazer backup de VMs Linux implantadas no Azure. Este artigo também inclui informações de solução de problemas.
ms.reviewer: anuragm
author: dcurwin
manager: carmonm
keywords: backup consistente com o aplicativo; backup de VM do Azure consistente com o aplicativo; Backup de VM do Linux; Backup do Azure
ms.service: backup
ms.topic: conceptual
ms.date: 01/12/2018
ms.author: dacurwin
ms.openlocfilehash: dc7745c7c1110bf3635b1621cecfd5e61488b9f9
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210403"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Backup consistente com o aplicativo de VMs Linux do Azure

Ao fazer backup de instantâneos de suas VMs, a consistência do aplicativo significa que seus aplicativos são iniciados quando as VMs são inicializadas após serem restauradas. Como você pode imaginar, a consistência do aplicativo é extremamente importante. Para garantir que suas VMs do Linux sejam consistentes com o aplicativo, você pode usar a estrutura de pré e pós-script do Linux para fazer backups consistentes com o aplicativo. A estrutura de pré e pós-script oferece suporte a máquinas virtuais Linux Azure Resource Manager implantadas. Scripts para consistência de aplicativos não dão suporte a máquinas virtuais implantadas Service Manager ou máquinas virtuais do Windows.

## <a name="how-the-framework-works"></a>Como funciona a estrutura

A estrutura fornece uma opção para executar pré-scripts personalizados e pós-scripts enquanto você estiver tirando instantâneos de VM. Os pré-scripts são executados logo antes de você pegar o instantâneo da VM e os pós-scripts são executados imediatamente depois que você pega o instantâneo da VM. Pré-scripts e pós-scripts fornecem a flexibilidade para controlar seu aplicativo e ambiente, enquanto você está levando instantâneos de VM.

Pré-scripts invocam APIs de aplicativo nativo, que desativam o IOs e liberam o conteúdo na memória para o disco. Essas ações garantem que o instantâneo seja consistente com o aplicativo. Os pós-scripts usam APIs de aplicativo nativo para descongelar o IOs, o que permite que o aplicativo retome as operações normais após o instantâneo da VM.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Etapas para configurar pré e pós-script

1. Entre como o usuário raiz para a VM Linux que você deseja fazer backup.

2. No [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), baixe **VMSnapshotScriptPluginConfig. JSON** e copie-o para a pasta **/etc/Azure** para todas as VMs das quais você deseja fazer backup. Se a pasta **/etc/Azure** não existir, crie-a.

3. Copie o script e o pós-script para seu aplicativo em todas as VMs que você planeja fazer backup. Você pode copiar os scripts para qualquer local na VM. Certifique-se de atualizar o caminho completo dos arquivos de script no arquivo **VMSnapshotScriptPluginConfig. JSON** .

4. Verifique as seguintes permissões para estes arquivos:

   - **VMSnapshotScriptPluginConfig.json**: Permissão "600." Por exemplo, somente o usuário "raiz" deve ter permissões de "leitura" e "gravação" para esse arquivo e nenhum usuário deve ter permissões de "execução".

   - **Arquivo de pré-script**: Permissão "700."  Por exemplo, somente o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" para esse arquivo.

   - **Pós-script** Permissão "700." Por exemplo, somente o usuário "raiz" deve ter permissões de "leitura", "gravação" e "execução" para esse arquivo.

   > [!Important]
   > A estrutura oferece aos usuários muito poder. Proteja a estrutura e certifique-se de que apenas o usuário "raiz" tenha acesso a arquivos de script e JSON críticos.
   > Se os requisitos não forem atendidos, o script não será executado, o que resultará em uma falha do sistema de arquivos e um backup inconsistente.
   >

5. Configure **VMSnapshotScriptPluginConfig. JSON** conforme descrito aqui:
    - **pluginName**: Deixe esse campo como está ou seus scripts podem não funcionar conforme o esperado.

    - **preScriptLocation**: Forneça o caminho completo do pré-script na VM que será submetida a backup.

    - **postScriptLocation**: Forneça o caminho completo do post-script na VM que será submetida a backup.

    - **preScriptParams**: Forneça os parâmetros opcionais que precisam ser passados para o pré-script. Todos os parâmetros devem estar entre aspas. Se você usar vários parâmetros, separe os parâmetros com uma vírgula.

    - **postScriptParams**: Forneça os parâmetros opcionais que precisam ser passados para o post-script. Todos os parâmetros devem estar entre aspas. Se você usar vários parâmetros, separe os parâmetros com uma vírgula.

    - **preScriptNoOfRetries**: Defina o número de vezes que o pré-script deve ser repetido se houver algum erro antes de encerrar. Zero significa apenas uma tentativa e nenhuma repetição se houver uma falha.

    - **postScriptNoOfRetries**:  Defina o número de vezes que o pós-script deve ser repetido se houver algum erro antes de encerrar. Zero significa apenas uma tentativa e nenhuma repetição se houver uma falha.

    - **timeoutInSeconds**: Especifique os tempos limite individuais para o pré-script e o pós-script (o valor máximo pode ser 1800).

    - **continueBackupOnFailure**: Defina esse valor como **verdadeiro** se desejar que o backup do Azure retorne a um backup consistente de sistema de arquivos/com falha em caso de falha de pré ou pós-script. Definir isso como **false** falha no backup em caso de falha de script (exceto quando você tem uma VM de disco único que volta para o backup consistente com falhas, independentemente dessa configuração).

    - **fsFreezeEnabled**: Especifique se o fsfreeze do Linux deve ser chamado enquanto você estiver obtendo o instantâneo da VM para garantir a consistência do sistema de arquivos. É recomendável manter essa configuração definida como **true** , a menos que seu aplicativo tenha uma dependência na desabilitação de fsfreeze.

6. A estrutura de script agora está configurada. Se o backup da VM já estiver configurado, o próximo backup invocará os scripts e disparará o backup consistente com o aplicativo. Se o backup da VM não estiver configurado, configure-o usando [fazer backup de máquinas virtuais do Azure para cofres dos serviços de recuperação.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Resolução de problemas

Certifique-se de adicionar o registro em log apropriado durante a gravação de seu script e pós-script e examine os logs de script para corrigir quaisquer problemas de script. Se você ainda tiver problemas ao executar scripts, consulte a tabela a seguir para obter mais informações.

| Erro | Mensagem de erro | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |O pré-script retornou um erro, portanto, o backup pode não ser consistente com o aplicativo.   | Examine os logs de falha do script para corrigir o problema.|  
|   Post-ScriptExecutionFailed |    O post-script retornou um erro que pode afetar o estado do aplicativo. |    Examine os logs de falha do script para corrigir o problema e verificar o estado do aplicativo. |
| Pre-ScriptNotFound |  O pré-script não foi encontrado no local especificado no arquivo de configuração **VMSnapshotScriptPluginConfig. JSON** . |   Verifique se o pré-script está presente no caminho especificado no arquivo de configuração para garantir o backup consistente com o aplicativo.|
| Post-ScriptNotFound | O post-script não foi encontrado no local especificado no arquivo de configuração **VMSnapshotScriptPluginConfig. JSON** . |   Verifique se o post-script está presente no caminho especificado no arquivo de configuração para garantir o backup consistente com o aplicativo.|
| IncorrectPluginhostFile | O arquivo **Pluginhost** , que vem com a extensão VmSnapshotLinux, está corrompido, portanto, o pré e o pós-script não podem ser executados e o backup não será consistente com o aplicativo. | Desinstale a extensão **VmSnapshotLinux** e ela será reinstalada automaticamente com o próximo backup para corrigir o problema. |
| IncorrectJSONConfigFile | O arquivo **VMSnapshotScriptPluginConfig. JSON** está incorreto, portanto, pré-script e pós-script não podem ser executados e o backup não será consistente com o aplicativo. | Baixe a cópia do [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e configure-a novamente. |
| InsufficientPermissionforPre-Script | Para executar scripts, o usuário "raiz" deve ser o proprietário do arquivo e o arquivo deve ter permissões "700" (ou seja, somente "proprietário" deve ter permissões de "leitura", "gravação" e "execução"). | Verifique se o usuário "raiz" é o "proprietário" do arquivo de script e se somente "proprietário" tem permissões de "leitura", "gravação" e "execução". |
| InsufficientPermissionforPost-Script | Para executar scripts, o usuário raiz deve ser o proprietário do arquivo e o arquivo deve ter permissões "700" (ou seja, somente "proprietário" deve ter permissões de "leitura", "gravação" e "execução"). | Verifique se o usuário "raiz" é o "proprietário" do arquivo de script e se somente "proprietário" tem permissões de "leitura", "gravação" e "execução". |
| Pre-ScriptTimeout | A execução do pré-script de backup consistente com o aplicativo esgotou o tempo limite. | Verifique o script e aumente o tempo limite no arquivo **VMSnapshotScriptPluginConfig. JSON** localizado em **/etc/Azure**. |
| Post-ScriptTimeout | A execução do pós-script de backup consistente com o aplicativo atingiu o tempo limite. | Verifique o script e aumente o tempo limite no arquivo **VMSnapshotScriptPluginConfig. JSON** localizado em **/etc/Azure**. |

## <a name="next-steps"></a>Passos Seguintes
[Configurar o backup da VM para um cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
