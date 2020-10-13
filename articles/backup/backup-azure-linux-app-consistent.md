---
title: Backups consistentes de aplicação dos LM Linux
description: Crie cópias de segurança consistentes com aplicações das suas máquinas virtuais Linux para o Azure. Este artigo explica a configuração da estrutura do script para apoiar os LM Linux implantados pelo Azure. Este artigo também inclui informações sobre resolução de problemas.
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 22053004026a2dd8976027359f11d50a5663b334
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88999245"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Cópia de segurança consistente com aplicações de VMs Linux do Azure

Ao tirar fotos de backup dos seus VMs, a consistência da aplicação significa que as suas aplicações começam quando o arranque dos VMs após ser restaurado. Como podem imaginar, a consistência da aplicação é extremamente importante. Para garantir que os seus VMs Linux são consistentes com a aplicação, pode utilizar a estrutura pré-script e pós-script do Linux para obter cópias de segurança consistentes com aplicações. A estrutura pré-script e pós-script suporta máquinas virtuais Linux implantadas pelo Azure Resource Manager. Os scripts para a consistência da aplicação não suportam máquinas virtuais implantadas pelo Service Manager ou máquinas virtuais do Windows.

## <a name="how-the-framework-works"></a>Como funciona o quadro

A estrutura oferece uma opção para executar pré-scripts e pós-scripts personalizados enquanto você está tirando fotos VM. Os pré-scripts são executados pouco antes de tirar a fotografia VM, e os post-scripts são executados imediatamente após tirar a foto de VM. Os pré-scripts e pós-scripts proporcionam a flexibilidade para controlar a sua aplicação e ambiente, enquanto está a tirar fotografias em VM.

Os pré-scripts invocam APIs de aplicação nativa, que quiesce os IOs, e lavam o conteúdo na memória para o disco. Estas ações garantem que o instantâneo é consistente com a aplicação. Os post-scripts utilizam APIs de aplicação nativa para descongelar os IOs, que permitem que a aplicação retome as operações normais após o instantâneo VM.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Passos para configurar pré-script e pós-script

1. Inscreva-se como utilizador principal do Linux VM que pretende fazer o back-up.

2. A partir do [GitHub,](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)faça o download **VMSnapshotScriptPluginConfig.js** e copie-o para a pasta **/etc/azure** para todos os VMs que pretende fazer cópias de trás. Se a pasta **/etc/azul** não existir, crie-a.

3. Copie o pré-script e o pós-script para a sua aplicação em todos os VMs que pretende fazer cópias. Pode copiar os scripts para qualquer local do VM. Certifique-se de atualizar o caminho completo dos ficheiros de scripts no **VMSnapshotScriptPluginConfig.jsno** ficheiro.

4. Certifique-se de que as seguintes permissões para estes ficheiros:

   - **VMSnapshotScriptPluginConfig.js:** Permissão "600". Por exemplo, apenas o utilizador "raiz" deve ter permissões de "ler" e "escrever" para este ficheiro, e nenhum utilizador deve ter permissões de "executar".

   - **Ficheiro pré-script:** Permissão "700".  Por exemplo, apenas o utilizador "raiz" deve ter permissões de "ler", "escrever" e "executar" para este ficheiro.

   - **Pós-script** Permissão "700". Por exemplo, apenas o utilizador "raiz" deve ter permissões de "ler", "escrever" e "executar" para este ficheiro.

   > [!IMPORTANT]
   > A estrutura dá aos utilizadores muito poder. Proteja a estrutura e garanta que apenas o utilizador "raiz" tem acesso a ficheiros JSON e scripts críticos.
   > Se os requisitos não forem cumpridos, o script não será executado, o que resulta numa falha no sistema de ficheiros e numa cópia de segurança inconsistente.
   >

5. Configurar **VMSnapshotScriptPluginConfig.jscomo** descrito aqui:
    - **pluginName**: Deixe este campo como está, ou os seus scripts podem não funcionar como esperado.

    - **pré-DescriLocation**: Forneça o caminho completo do pré-script no VM que será apoiado.

    - **pós-Descrição**: Forneça o caminho completo do pós-script no VM que será apoiado.

    - **pré-ScriptParams**: Forneça os parâmetros opcionais que precisam de ser passados para o pré-script. Todos os parâmetros devem estar em aspas. Se utilizar vários parâmetros, separe os parâmetros com uma vírgula.

    - **postScriptParams**: Forneça os parâmetros opcionais que precisam de ser passados para o pós-script. Todos os parâmetros devem estar em aspas. Se utilizar vários parâmetros, separe os parâmetros com uma vírgula.

    - **preScriptNoOfRetries**: Desabre o número de vezes que o pré-script deve ser novamente julgado se houver algum erro antes de terminar. Zero significa apenas uma tentativa e nenhuma tentativa se houver um fracasso.

    - **postScriptNoOfRetries**: Desabre o número de vezes que o pós-script deve ser novamente julgado se houver algum erro antes de terminar. Zero significa apenas uma tentativa e nenhuma tentativa se houver um fracasso.

    - **timeoutInSegundos**: Especifique os intervalos individuais para o pré-script e o pós-script (o valor máximo pode ser de 1800).

    - **continuarBackupOnFailure**: Defina este valor como **verdadeiro** se quiser que o Azure Backup recue para um sistema de ficheiros consistente/falha de backup consistente se o pré-script ou o pós-script falharem. Definir isto para **falsa** falha na cópia de segurança se houver uma falha de script (exceto quando tiver um VM de disco único que recue para uma cópia de segurança consistente, independentemente desta definição). Quando o valor **continueBackupOnFailure** for definido como falso, se a cópia de segurança falhar, a operação de backup será novamente tentada com base numa lógica de retenção no serviço (para o número de tentativas estipulado).

    - **fsFreezeEnabled**: Especifique se o linux fsfreeze deve ser chamado enquanto estiver a tirar a imagem VM para garantir a consistência do sistema de ficheiros. Recomendamos manter esta definição definida de **forma verdadeira,** a menos que a sua aplicação tenha uma dependência de desativar o fsfreeze.

    - **ScriptsExecutionPollTimeSeconds**: Desa esta medida a extensão tem de dormir entre cada sondagem e a execução do script. Por exemplo, se o valor for 2, a extensão verifica se a execução do script pré/post foi concluída a cada 2 segundos. O valor mínimo e máximo que pode ter é de 1 e 5, respectivamente. O valor deve ser estritamente um inteiro.

6. A estrutura do guião está agora configurada. Se a cópia de segurança VM já estiver configurada, a próxima cópia de segurança invoca os scripts e aciona a cópia de segurança consistente da aplicação. Se a cópia de segurança VM não estiver configurada, configure-a utilizando [máquinas virtuais de Back up Azure para cofres dos Serviços de Recuperação.](./backup-azure-vms-first-look-arm.md)

## <a name="troubleshooting"></a>Resolução de problemas

Certifique-se de que adiciona registos adequados enquanto escreve o seu pré-script e pós-script e revê os registos do seu script para corrigir quaisquer problemas de script. Se ainda tiver problemas em executar scripts, consulte a tabela seguinte para obter mais informações.

| Erro | Mensagem de erro | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Pré-ScriptExecutionFailed |O pré-script devolveu um erro, por isso a cópia de segurança pode não ser consistente com a aplicação.| Veja os registos de falha para o seu script corrigir o problema.|  
|Pós-ScriptExecutionFailed |O pós-script devolveu um erro que pode afetar o estado da aplicação. |Veja os registos de falha para o seu script para corrigir o problema e verificar o estado da aplicação. |
| Pré-ScriptNotFound |O pré-guião não foi encontrado no local especificado no **VMSnapshotScriptPluginConfig.jsno** ficheiro config. |Certifique-se de que o pré-script está presente no caminho especificado no ficheiro config para garantir uma cópia de segurança consistente com a aplicação.|
| Pós-ScriptNotFound |O pós-script não foi encontrado no local especificado no **VMSnapshotScriptPluginConfig.jsno** ficheiro config. |Certifique-se de que o pós-script está presente no caminho especificado no ficheiro config para garantir uma cópia de segurança consistente com a aplicação.|
| IncorretaPluginhostFile |O ficheiro **Pluginhost,** que vem com a extensão VmSnapshotLinux, está corrompido, pelo que o pré-script e o pós-script não podem ser executados e a cópia de segurança não será consistente com a aplicação.| Desinstale a extensão **VmSnapshotLinux** e será automaticamente reinstalada com a próxima cópia de segurança para corrigir o problema. |
| IncorrectJSONConfigFile | O **VMSnapshotScriptPluginConfig.jsno** ficheiro está incorreto, por isso o pré-script e o pós-script não podem ser executados e a cópia de segurança não será consistente com a aplicação. | Descarregue a cópia do [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e configuure-a novamente. |
| InsufficientPermissionforPre-Script | Para a execução de scripts, o utilizador "raiz" deve ser o proprietário do ficheiro e o ficheiro deve ter permissões de "700" (ou seja, apenas "proprietário" deve ter permissões de "ler", "escrever" e "executar"). | Certifique-se de que o utilizador "raiz" é o "proprietário" do ficheiro de script e que apenas "proprietário" tem permissões de "ler", "escrever" e "executar". |
| InsufficientPermissionforPost-Script | Para a execução de scripts, o utilizador raiz deve ser o proprietário do ficheiro e o ficheiro deve ter permissões de "700" (ou seja, apenas "proprietário" deve ter "ler", "escrever" e "executar" permissões). | Certifique-se de que o utilizador "raiz" é o "proprietário" do ficheiro de script e que apenas "proprietário" tem permissões de "ler", "escrever" e "executar". |
| Pré-ScriptTimeout | A execução do pré-script de backup consistente da aplicação. | Verifique o script e aumente o tempo limite no **VMSnapshotScriptPluginConfig.jsno** ficheiro localizado em **/etc/azure**. |
| Pós-ScriptTimeout | A execução do backup consistente de aplicação pós-script cronometrado. | Verifique o script e aumente o tempo limite no **VMSnapshotScriptPluginConfig.jsno** ficheiro localizado em **/etc/azure**. |

## <a name="next-steps"></a>Passos seguintes

[Configure o backup VM para um cofre dos Serviços de Recuperação](./backup-azure-vms-first-look-arm.md)
