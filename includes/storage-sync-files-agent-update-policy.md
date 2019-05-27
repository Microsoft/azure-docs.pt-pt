---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 4f59f68c1598f737ea7cb3a0e8046fc0779ed9d3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113796"
---
O agente de sincronização de ficheiros do Azure é atualizado regularmente para adicionar novas funcionalidades e para resolver problemas. Recomendamos que configure o Microsoft Update para obter atualizações para o agente de sincronização de ficheiros do Azure, como eles estão disponíveis.

#### <a name="major-vs-minor-agent-versions"></a>Principais vs. as versões do agente secundárias
* Muitas vezes, as versões do agente principais contêm novos recursos e tem um número crescente como a primeira parte do número de versão. Por exemplo: \*2.\*.\*\*
* As versões do agente secundária são também denominadas "patches" e são lançadas com mais frequência do que as versões principais. Geralmente contêm correções de erros e melhorias menores, mas não existem novas funcionalidades. Por exemplo: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Caminhos de atualização
Existem quatro aprovação e teste de formas de instalar as atualizações de agente do Azure File Sync. 
1. **(Preferencial) Configure o Microsoft Update para transferir e instalar as atualizações do agente automaticamente.**  
    Recomendamos obter sempre todas as atualizações de sincronização de ficheiros do Azure para garantir que tem acesso aos mais recentes correções para o agente de servidor. Microsoft Update torna esse processo integrada, ao automaticamente transferir e instalar atualizações para.
2. **Utilize AfsUpdater.exe para transferir e instalar as atualizações do agente.**  
    O AfsUpdater.exe está localizado no diretório de instalação do agente. Clique duas vezes no executável para transferir e instalar as atualizações do agente. 
3. **Aplicar um patch um agente de sincronização de ficheiros do Azure existente ao utilizar um ficheiro de patch do Microsoft Update, ou um. msp executável. O pacote de atualização mais recente do Azure File Sync pode ser transferido a partir da [catálogo Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Executar um. msp executável atualizará sua instalação do Azure File Sync com o mesmo método utilizado automaticamente pelo Microsoft Update no caminho de atualização anterior. Aplicar um patch do Microsoft Update irá efetuar uma atualização no local de uma instalação de sincronização de ficheiros do Azure.
4. **Transferir o instalador do agente do Azure File Sync mais recentes do [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Para atualizar uma instalação de agente de sincronização de ficheiros do Azure existente, desinstale a versão mais antiga e, em seguida, instalar a versão mais recente a partir do instalador que transferiu. O registo do servidor, grupos de sincronização e qualquer outra configuração é mantidas pelo instalador do Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Gerenciamento de ciclo de vida de automática do agente
Com a versão do agente 6, a equipe de sincronização de ficheiros introduziu uma funcionalidade de atualização automática do agente. Pode selecionar qualquer um dos dois modos e especifique uma janela de manutenção no qual deve ser tentada a atualização no servidor. Esta funcionalidade foi projetada para ajudá-lo com a gestão de ciclo de vida de agente fornecendo um guardrail que impede o agente de expiração ou permitindo uma e sem complicações, mantenha-se a definição atual.
1. O **predefinição** irá tentar impedir que o agente de expiração. Dentro de 21 dias após a data de expiração de postagem de um agente, o agente irá tentar a atualização automática. Iniciará uma tentativa de atualizar uma vez por semana dentro de 21 dias antes da expiração e na janela de manutenção selecionado. **Esta opção não elimina a necessidade de colocar patches comuns do Microsoft Update.**
2. Opcionalmente, pode selecionar que o agente irão atualizar automaticamente em si, assim como uma nova versão de agente fica disponível. Isso também ocorrem durante a janela de manutenção selecionado e permitir que o seu servidor beneficiar de aprimoramentos e novos recursos assim que estiverem em disponibilidade geral. Esta é a definição recomendada, livre que fornecerão as versões do agente principais, bem como patches regulares de atualização para o seu servidor.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantias de gerenciamento de ciclo de vida e a alteração do agente
O Azure File Sync é um serviço em nuvem, que cria continuamente novos recursos e melhorias. Isso significa que uma versão de agente do Azure File Sync específica só pode ser suportada por um período limitado. Para facilitar a implementação, as seguintes regras de garantem que tem suficiente tempo e a notificação para acomodar o agente atualizações/melhoramentos em seu processo de gestão de alteração:

- As versões do agente principais são suportadas para, pelo menos, seis meses a contar da data de lançamento inicial.
- Garantimos que haja uma sobreposição de, pelo menos, três meses entre o suporte das versões principais do agente. 
- São emitidos avisos para servidores registados, usando um agente de expiradas em breve-para-ser, pelo menos, três meses antes da expiração. Pode verificar se um servidor registado está a utilizar uma versão mais antiga do agente na secção de servidores registados, de um serviço de sincronização de armazenamento.
- O tempo de vida de uma versão de agente secundária está associado para a versão principal associada. Por exemplo, quando for lançada a versão de agente 3.0, as versões do agente 2. \* serão todos ser definido para expirar em conjunto.

> [!Note]
> Instalar uma versão do agente com um aviso de expiração irá apresentar um aviso, mas tenha êxito. A tentar instalar ou ligar a uma versão de agente expirada, não é suportada e será bloqueada.
