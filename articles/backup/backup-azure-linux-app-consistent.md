---
title: 'Cópia de segurança do Azure: cópias de segurança consistentes com aplicações de VMs do Linux'
description: Crie cópias de segurança consistentes com aplicações das suas máquinas virtuais do Linux no Azure. Este artigo explica a configuração da estrutura de script para fazer uma cópia de segurança de VMs do Linux implementadas no Azure. Este artigo também contém informações de resolução de problemas.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: cópia de segurança consistente com a aplicação; cópias de segurança de VM do Azure consistentes; Cópia de segurança de VM do Linux; Cópia de segurança do Azure
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641131"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Cópias de segurança consistentes de VMs do Linux do Azure

Quando a obtenção de instantâneos de cópia de segurança das suas VMs, consistência de aplicação significa que seus aplicativos iniciar quando as VMs de arranque depois de a ser restaurada. Como pode imaginar, consistência de aplicação é extremamente importante. Para garantir que suas VMs do Linux são consistente, que pode utilizar a estrutura de script anterior e script posterior do Linux para efetuar cópias de segurança consistentes com aplicações da aplicação. A estrutura do script prévio e o script posterior suporta máquinas virtuais do Linux implementadas no Azure Resource Manager. Scripts para consistência de aplicação não suportam máquinas virtuais implementadas com o Service Manager ou máquinas de virtuais do Windows.

## <a name="how-the-framework-works"></a>Como funciona o framework

O framework fornece uma opção para executar pré-scripts de personalizados e pós-scripts de enquanto estiver a fazer instantâneos de VM. Pré-scripts de executam imediatamente antes de tirar o instantâneo da VM e os pós-scripts de executados imediatamente após a tirar o instantâneo VM. Scripts de pré e pós-scripts fornecem a flexibilidade para controlar a sua aplicação e o ambiente, enquanto estiver a fazer instantâneos de VM.

Pré-scripts de invocar a aplicação nativa APIs, qual Silenciar os IOs e liberar o conteúdo dentro da memória para o disco. Estas ações Certifique-se que o instantâneo é consistente com a aplicação. Pós-scripts utilizam APIs de aplicação nativa descongelar do IOs, que permite que o aplicativo para retomar as operações normais após o instantâneo VM.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Passos para configurar o script prévio e o script posterior

1. Inicie sessão como utilizador raiz para a VM do Linux que pretende criar cópias de segurança.

2. Partir [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig), transfira **VMSnapshotScriptPluginConfig.json** e copie-o para o **/etc/azure** pasta para todas as VMs que pretende criar cópias de segurança. Se o **/etc/azure** pasta não existir, crie-o.

3. Copie o script prévio e o script posterior para a sua aplicação em todas as VMs que pretende criar cópias de segurança. Pode copiar os scripts em qualquer local na VM. Certifique-se de que o caminho completo dos ficheiros de script de atualização do **VMSnapshotScriptPluginConfig.json** ficheiro.

4. Certifique-se as seguintes permissões para esses arquivos:

   - **VMSnapshotScriptPluginConfig.json**: Permissão "600." Por exemplo, apenas os utilizadores de "raiz" devem ter permissões de "leitura" e "escrever" a este ficheiro, e nenhum utilizador deve ter permissões de "execução".

   - **Ficheiro de script prévio**: Permissão "700."  Por exemplo, o utilizador de "raiz" apenas deve ter "ler", "gravação" e "executar" permissões para este ficheiro.

   - **O script posterior** permissão "700." Por exemplo, o utilizador de "raiz" apenas deve ter "ler", "gravação" e "executar" permissões para este ficheiro.

   > [!Important]
   > O framework fornece aos usuários muita energia. Proteger o framework e certifique-se apenas de "raiz" utilizador tem acesso ao JSON crítico e arquivos de script.
   > Se não forem cumpridos os requisitos, o script não será executado, que resulta numa falha de sistema de ficheiros e a cópia de segurança inconsistente.
   >

5. Configurar **VMSnapshotScriptPluginConfig.json** conforme descrito aqui:
    - **pluginName**: Deixe este campo como está, ou os scripts podem não funcionar conforme esperado.

    - **preScriptLocation**: Forneça o caminho completo do script prévio na VM que vai ser efetuada a cópia de segurança.

    - **postScriptLocation**: Forneça o caminho completo do script posterior na VM que vai ser efetuada a cópia de segurança.

    - **preScriptParams**: Forneça os parâmetros opcionais que precisam ser passados para o script prévio. Todos os parâmetros devem ser aspas. Se usar vários parâmetros, separe-os com uma vírgula.

    - **postScriptParams**: Forneça os parâmetros opcionais que precisam ser passados para o script posterior. Todos os parâmetros devem ser aspas. Se usar vários parâmetros, separe-os com uma vírgula.

    - **preScriptNoOfRetries**: Defina o número de vezes que o script prévio deve ser repetido se houver qualquer erro antes de terminar. Zero try de apenas um meio e nenhuma repetição se ocorrer uma falha.

    - **postScriptNoOfRetries**:  Defina o número de vezes que o script posterior deve ser repetido se houver qualquer erro antes de terminar. Zero try de apenas um meio e nenhuma repetição se ocorrer uma falha.

    - **timeoutInSeconds**: Especifica tempos limite individual para o script prévio e o script posterior (o valor máximo pode ser 1800).

    - **continueBackupOnFailure**: Definir este valor como **true** se desejar Backup do Azure para reverter para um backup consistente/falhas consistente do sistema de ficheiros se o script prévio ou falha do script posterior. Definir este tipo como **false** falhar a cópia de segurança em caso de falha de script (exceto se tiver de VM de disco único que faz fallback para a cópia de segurança consistentes de falhas independentemente desta definição).

    - **fsFreezeEnabled**: Especifique se Linux fsfreeze deve ser chamado enquanto estiver a fazer o instantâneo VM para garantir a consistência do sistema de ficheiros. Recomendamos que mantenha esta definição definida como **true** , a menos que seu aplicativo tem uma dependência sobre como desabilitar fsfreeze.

6. A estrutura de script está agora configurada. Se a cópia de segurança da VM já está configurada, o próximo backup invoca os scripts e aciona cópias de segurança consistentes. Se a cópia de segurança VM não estiver configurada, configure-o utilizando [cópia de segurança máquinas virtuais do Azure para cofres dos serviços de recuperação.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Resolução de problemas

Certifique-se de que adicione adequados de registro ao escrever o script prévio e o script posterior e rever os registos de script para corrigir problemas de script. Se ainda tiver problemas para executar scripts, consulte a tabela seguinte para obter mais informações.

| Erro | Mensagem de erro | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |O script prévio devolveu um erro, para que a cópia de segurança poderá não ser consistentes.   | Consulte os registos de falha para o seu script corrigir o problema.|  
|   Post-ScriptExecutionFailed |    O script posterior devolveu um erro que pode afetar o estado da aplicação. |    Consulte os registos de falha para o seu script corrigir o problema e verificar o estado da aplicação. |
| Pre-ScriptNotFound |  O script prévio não foi encontrado na localização especificada no **VMSnapshotScriptPluginConfig.json** ficheiro de configuração. |   Certifique-se de que esse script prévio está presente no caminho especificado no ficheiro de configuração para garantir que a cópia de segurança consistentes com aplicações.|
| Post-ScriptNotFound | O script posterior não foi encontrado na localização especificada no **VMSnapshotScriptPluginConfig.json** ficheiro de configuração. |   Certifique-se de que esse script posterior está presente no caminho especificado no ficheiro de configuração para garantir que a cópia de segurança consistentes com aplicações.|
| IncorrectPluginhostFile | O **Pluginhost** ficheiro, que vem com a extensão VmSnapshotLinux, está danificado, pelo que não é possível executar o script prévio e o script posterior e a cópia de segurança não será consistente com a aplicação. | Desinstalar o **VmSnapshotLinux** extensão e ele automaticamente serão reinstalado com a cópia de segurança seguinte para corrigir o problema. |
| IncorrectJSONConfigFile | O **VMSnapshotScriptPluginConfig.json** ficheiro está incorreta, portanto, o script prévio e não é possível executar o script posterior e a cópia de segurança não será consistente com a aplicação. | Transferir a cópia a partir [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e configurá-la novamente. |
| InsufficientPermissionforPre-Script | Para executar scripts, o utilizador "raiz" deve ser o proprietário do ficheiro e o ficheiro deve ter permissões de "700" (ou seja, apenas "proprietário" deve ter "ler", "gravação" e permissões de "execução"). | Certifique-se de que o utilizador "raiz" é o "proprietário" do ficheiro de script e que apenas "proprietário" tem "permissões de leitura", "gravação" e "execução". |
| InsufficientPermissionforPost-Script | Para executar scripts, o utilizador raiz deve ser o proprietário do ficheiro e o ficheiro deve ter permissões de "700" (ou seja, apenas "proprietário" deve ter "ler", "gravação" e permissões de "execução"). | Certifique-se de que o utilizador "raiz" é o "proprietário" do ficheiro de script e que apenas "proprietário" tem "permissões de leitura", "gravação" e "execução". |
| Pre-ScriptTimeout | A execução do script prévio cópia de segurança consistentes com a aplicação excedeu o tempo. | Verifique o script e aumentar o tempo limite no **VMSnapshotScriptPluginConfig.json** ficheiro que está localizado na **/etc/azure**. |
| Post-ScriptTimeout | A execução do pós-script cópia de segurança consistentes com a aplicação excedeu o tempo limite. | Verifique o script e aumentar o tempo limite no **VMSnapshotScriptPluginConfig.json** ficheiro que está localizado na **/etc/azure**. |

## <a name="next-steps"></a>Passos Seguintes
[Configurar cópia de segurança VM para um cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
