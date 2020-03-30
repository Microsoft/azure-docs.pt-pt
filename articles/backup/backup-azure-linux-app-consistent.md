---
title: Backups consistentes com aplicações de VMs Linux
description: Crie cópias de segurança consistentes com aplicações das suas máquinas virtuais Linux para o Azure. Este artigo explica configurar a estrutura do script para apoiar os VMs Linux implantados pelo Azure. Este artigo também inclui informações sobre resolução de problemas.
ms.reviewer: anuragm
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 36eeb9f63c67a01bf37412101e23be035596de94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173010"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Backup consistente com aplicações de VMs Azure Linux

Ao tirar fotografias de backup dos seus VMs, a consistência da aplicação significa que as suas aplicações começam quando as VMs arrancam depois de restauradas. Como pode imaginar, a consistência da aplicação é extremamente importante. Para garantir que os seus VMs Linux são consistentes com aplicações, pode utilizar a estrutura pré-script e pós-script do Linux para obter backups consistentes com aplicações. A estrutura pré-script e pós-script suporta máquinas virtuais Linux implantadas pelo Gestor de Recursos Azure. Scripts para consistência da aplicação não suportam máquinas virtuais implementadas pelo Gestor de Serviços ou máquinas virtuais do Windows.

## <a name="how-the-framework-works"></a>Como funciona o quadro

A estrutura fornece uma opção para executar pré-scripts e pós-scripts personalizados enquanto você está tirando fotos vM. Os pré-scripts são executados antes de tirar a foto do VM, e os pós-scripts são executados imediatamente após tirar a foto do VM. Pré-scripts e pós-scripts proporcionam a flexibilidade para controlar a sua aplicação e ambiente, enquanto você está tirando fotos vM.

Os pré-scripts invocam APIs de aplicação nativa, que quiesce os IOs, e descarregam o conteúdo na memória para o disco. Estas ações garantem que o instantâneo é consistente. Os pós-scripts utilizam APIs de aplicação nativa para descongelar os IOs, que permitem que a aplicação retome as operações normais após o instantâneo vm.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Passos para configurar pré-script e pós-script

1. Inscreva-se como o utilizador principal do VM Linux que pretende fazer o back-up.

2. A partir do [GitHub,](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)baixe **o VMSnapshotScriptPluginConfig.json** e copie-o para a pasta **/etc/azure** para todos os VMs que deseja fazer. Se a pasta **/etc/azure** não existir, crie-a.

3. Copie o pré-script e o pós-script para a sua aplicação em todos os VMs que planeia fazer cópias de tempo. Pode copiar os scripts para qualquer local do VM. Certifique-se de atualizar o caminho completo dos ficheiros do script no ficheiro **VMSnapshotScriptPluginConfig.json.**

4. Certifique-se das seguintes permissões para estes ficheiros:

   - **VMSnapshotScriptPluginConfig.json**: Permissão "600". Por exemplo, apenas o utilizador "raiz" deve ter permissões de "ler" e "escrever" para este ficheiro, e nenhum utilizador deve ter permissões de "executar".

   - **Ficheiro pré-script**: Permissão "700".  Por exemplo, apenas o utilizador "raiz" deve ter permissões "ler", "escrever" e "executar" permissões a este ficheiro.

   - **Pós-script** Permissão "700". Por exemplo, apenas o utilizador "raiz" deve ter permissões "ler", "escrever" e "executar" permissões a este ficheiro.

   > [!IMPORTANT]
   > O quadro dá aos utilizadores muita energia. Proteja a estrutura e garanta que apenas o utilizador "raiz" tem acesso a ficheiros críticos de JSON e script.
   > Se os requisitos não forem cumpridos, o guião não será executado, o que resulta numa falha no sistema de ficheiros e numa cópia de segurança inconsistente.
   >

5. Configure **VMSnapshotScriptPluginConfig.json** como descrito aqui:
    - **pluginName**: Deixe este campo como está, ou os seus scripts podem não funcionar como esperado.

    - **pré-ScriptLocation**: Forneça o caminho completo do pré-script no VM que vai ser apoiado.

    - **postScriptLocation**: Forneça o caminho completo do post-script no VM que vai ser apoiado.

    - **preScriptParams**: Forneça os parâmetros opcionais que precisam de ser passados para o pré-script. Todos os parâmetros devem estar em aspas. Se utilizar vários parâmetros, separe os parâmetros com uma vírem.

    - **postScriptParams**: Forneça os parâmetros opcionais que precisam de ser passados para o pós-script. Todos os parâmetros devem estar em aspas. Se utilizar vários parâmetros, separe os parâmetros com uma vírem.

    - **preScriptNoOfRetries**: Desative o número de vezes que a pré-escrita deve ser novamente tentada se houver algum erro antes de terminar. Zero significa apenas uma tentativa e nenhuma retentativa se houver uma falha.

    - **postScriptNoOfRetries**: Definir o número de vezes que o pós-script deve ser novamente experimentado se houver algum erro antes de terminar. Zero significa apenas uma tentativa e nenhuma retentativa se houver uma falha.

    - **timeoutInSeconds**: Especifique os intervalos individuais para o pré-script e o pós-script (o valor máximo pode ser de 1800).

    - **continueBackupOnFailure**: Desative este valor para **o verdadeiro** caso pretenda que o Azure Backup recue para um sistema de ficheiros consistente/contraído uma cópia de segurança consistente/de supor se o pré-script ou o pós-script falhar. A definição disto como **falsa** falha a cópia de segurança em caso de falha no script (exceto quando tiver um VM de disco único que cai para trás para uma cópia de segurança consistente em queda, independentemente desta definição).

    - **fsFreezeEnabled**: Especifique se o linux fsfreeze deve ser chamado enquanto estiver a tirar o instantâneo vM para garantir a consistência do sistema de ficheiros. Recomendamos que esta definição seja **verdadeira,** a menos que a sua aplicação tenha uma dependência de descongelação.

    - **ScriptsExecutionPollTimeSeconds**: Desdefinido o tempo que a extensão tem de dormir entre cada sondagem e a execução do script. Por exemplo, se o valor for 2, a extensão verifica se a execução pré/post script foi concluída a cada 2 segundos. O valor mínimo e máximo que pode tomar é de 1 e 5, respectivamente. O valor deve ser estritamente um inteiro.

6. O quadro do guião está agora configurado. Se a cópia de segurança VM já estiver configurada, a próxima cópia de segurança invoca os scripts e aciona a cópia de segurança consistente com aplicações. Se a cópia de segurança VM não estiver configurada, configure-a utilizando [máquinas virtuais Back up Azure para cofres de Serviços de Recuperação.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Resolução de problemas

Certifique-se de que adiciona o registo adequado enquanto escreve o seu pré-script e pós-script e reveja os registos do script para corrigir quaisquer problemas de script. Se ainda tiver problemas em executar scripts, consulte a tabela seguinte para obter mais informações.

| Erro | Mensagem de erro | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Pré-ScriptExecutionFailed |O pré-script devolveu um erro, por isso a cópia de segurança pode não ser consistente com a aplicação.| Veja os registos de falha do seu script para corrigir o problema.|  
|Pós-ScriptExecutionFailed |O pós-script devolveu um erro que pode ter impacto no estado da aplicação. |Veja os registos de falha do seu script para corrigir o problema e verifique o estado de aplicação. |
| Pré-ScriptNotFound |O pré-script não foi encontrado no local especificado no ficheiro **config VMSnapshotScriptPluginConfig.json.** |Certifique-se de que o pré-script está presente no caminho especificado no ficheiro config para garantir a cópia de segurança consistente com a aplicação.|
| Pós-ScriptNotFound |O pós-script não foi encontrado no local especificado no ficheiro **config VMSnapshotScriptPluginConfig.json.** |Certifique-se de que o pós-script está presente no caminho especificado no ficheiro config para garantir a cópia de segurança consistente com a aplicação.|
| Ficheiro de anfitriões Plugin |O ficheiro **Pluginhost,** que vem com a extensão VmSnapshotLinux, é corrompido, por isso o pré-script e o pós-script não podem ser executados e a cópia de segurança não será consistente com aplicações.| Desinstale a extensão **VmSnapshotLinux** e será automaticamente reinstalada com a próxima cópia de segurança para corrigir o problema. |
| Ficheiro IncorrectOJSONConfigFile | O ficheiro **VMSnapshotScriptPluginConfig.json** está incorreto, pelo que o pré-script e o pós-script não podem ser executados e a cópia de segurança não será consistente com a aplicação. | Descarregue a cópia do [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e configure-a novamente. |
| InsuficientePerperperspara Pré-Script | Para executar scripts, o utilizador "raiz" deve ser o proprietário do ficheiro e o ficheiro deve ter permissões "700" (ou seja, apenas "proprietário" deve ter permissões "ler", "escrever" e "executar"). | Certifique-se de que o utilizador "raiz" é o "proprietário" do ficheiro script e que apenas "proprietário" tem permissões de "ler", "escrever" e "executar". |
| InsuficientePerpersperspara Post-Script | Para executar scripts, o utilizador raiz deve ser o proprietário do ficheiro e o ficheiro deve ter permissões "700" (ou seja, apenas "proprietário" deve ter permissões "ler", "escrever" e "executar"). | Certifique-se de que o utilizador "raiz" é o "proprietário" do ficheiro script e que apenas "proprietário" tem permissões de "ler", "escrever" e "executar". |
| Pré-ScriptTimeout | A execução do pré-script de reserva consistente com aplicação. | Verifique o script e aumente o tempo de tempo no ficheiro **VMSnapshotScriptPluginConfig.json** que está localizado em **/etc/azure**. |
| Pós-ScriptTimeout | A execução do pós-script de cópia de segurança consistente com aplicação foi cronometrada. | Verifique o script e aumente o tempo de tempo no ficheiro **VMSnapshotScriptPluginConfig.json** que está localizado em **/etc/azure**. |

## <a name="next-steps"></a>Passos seguintes

[Configure cópia de segurança VM para um cofre de Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
