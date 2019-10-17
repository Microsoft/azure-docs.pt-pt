---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: 02e9553b9704c96794e0c1113ab3e06458f0f7c8
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391753"
---
O agente de Sincronização de Arquivos do Azure é atualizado regularmente para adicionar novas funcionalidades e resolver problemas. Recomendamos que você configure Microsoft Update para obter atualizações para o agente de Sincronização de Arquivos do Azure como estão disponíveis.

#### <a name="major-vs-minor-agent-versions"></a>Versões do agente principal vs. secundária
* As versões principais do agente geralmente contêm novos recursos e têm um número crescente como a primeira parte do número de versão. Por exemplo: \*2. \*. \* @ no__t-3
* As versões secundárias do agente também são chamadas de "patches" e liberadas com mais frequência do que as versões principais. Geralmente, eles contêm correções de bugs e melhorias menores, mas não há novos recursos. Por exemplo: \* @ no__t-1.3. \* @ no__t-3

#### <a name="upgrade-paths"></a>Caminhos de atualização
Há quatro maneiras aprovadas e testadas para instalar as atualizações do agente de Sincronização de Arquivos do Azure. 
1. **Preferencial Configure o Microsoft Update para baixar e instalar atualizações de agente automaticamente.**  
    É sempre recomendável usar cada atualização de Sincronização de Arquivos do Azure para garantir que você tenha acesso às correções mais recentes para o agente do servidor. Microsoft Update torna esse processo contínuo, baixando e instalando automaticamente as atualizações para você.
2. **Use AfsUpdater. exe para baixar e instalar atualizações de agente.**  
    O AfsUpdater. exe está localizado no diretório de instalação do agente. Clique duas vezes no executável para baixar e instalar atualizações de agente. 
3. **Corrija um agente de Sincronização de Arquivos do Azure existente usando um arquivo de patch Microsoft Update ou um executável. msp. O pacote de atualização mais recente do Sincronização de Arquivos do Azure pode ser baixado do [Catálogo de Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    A execução de um executável. msp atualizará sua instalação do Sincronização de Arquivos do Azure com o mesmo método usado automaticamente pelo Microsoft Update no caminho de atualização anterior. A aplicação de um patch de Microsoft Update executará uma atualização in-loco de uma instalação do Sincronização de Arquivos do Azure.
4. **Baixe o instalador do agente de Sincronização de Arquivos do Azure mais recente no [centro de download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Para atualizar uma instalação existente do agente do Sincronização de Arquivos do Azure, desinstale a versão mais antiga e instale a versão mais recente do instalador baixado. O registro do servidor, os grupos de sincronização e as outras configurações são mantidos pelo instalador do Sincronização de Arquivos do Azure.

#### <a name="automatic-agent-lifecycle-management"></a>Gerenciamento automático do ciclo de vida do agente
Com o Agent versão 6, a equipe de sincronização de arquivos introduziu um recurso de atualização automática de agente. Você pode selecionar um dos dois modos e especificar uma janela de manutenção na qual a atualização deve ser tentada no servidor. Esse recurso foi criado para ajudá-lo com o gerenciamento do ciclo de vida do agente, fornecendo um Guardrail que impede o agente de expiração ou permitindo uma configuração sem complicações, mantenha-se atualizado.
1. A **configuração padrão** tentará impedir que o agente expire. Dentro de 21 dias da data de expiração lançada de um agente, o agente tentará fazer a atualização automática. Ele iniciará uma tentativa de atualizar uma vez por semana em 21 dias antes da expiração e na janela de manutenção selecionada. **Essa opção não elimina a necessidade de fazer patches de Microsoft Update regulares.**
1. Opcionalmente, você pode selecionar se o agente será atualizado automaticamente assim que uma nova versão do agente se tornar disponível (atualmente não aplicável a servidores clusterizados). Essa atualização ocorrerá durante a janela de manutenção selecionada e permitirá que o servidor se beneficie dos novos recursos e aprimoramentos assim que eles forem disponibilizados para o público geral. Essa é a configuração recomendada e sem preocupações que fornecerá as principais versões do agente, bem como patches de atualização regulares para o servidor. Cada agente lançado está em qualidade de GA. Se você selecionar essa opção, a Microsoft irá comprovar a versão mais recente do agente para você. Os servidores clusterizados são excluídos. Após a conclusão do processamento, o agente também ficará disponível no [centro de download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/Agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Alterando a configuração de atualização automática

As instruções a seguir descrevem como alterar as configurações depois de concluir o instalador, se você precisar fazer alterações.

Abra um console do PowerShell e navegue até o diretório em que você instalou o agente de sincronização e, em seguida, importe os cmdlets do servidor. Por padrão, isso seria semelhante a este:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name \StorageSync.Management.ServerCmdlets.dll
```

Você pode executar `Get-StorageSyncAgentAutoUpdatePolicy` para verificar a configuração de política atual e determinar se deseja alterá-la.

Para alterar a configuração de política atual para a faixa de atualização atrasada, você pode usar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Para alterar a configuração de política atual para a faixa de atualização imediata, você pode usar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantias de ciclo de vida do agente e gerenciamento de alterações
Sincronização de Arquivos do Azure é um serviço de nuvem, que apresenta continuamente novos recursos e aprimoramentos. Isso significa que uma versão específica do agente de Sincronização de Arquivos do Azure só pode ter suporte por um tempo limitado. Para facilitar sua implantação, as regras a seguir garantem que você tenha tempo e notificações suficientes para acomodar atualizações/upgrades do agente em seu processo de gerenciamento de alterações:

- As versões principais do agente têm suporte por pelo menos seis meses a partir da data da versão inicial.
- Garantimos que há uma sobreposição de pelo menos três meses entre o suporte das principais versões do agente. 
- Os avisos são emitidos para servidores registrados usando um agente em breve para ser expirado pelo menos três meses antes da expiração. Você pode verificar se um servidor registrado está usando uma versão mais antiga do agente na seção servidores registrados de um serviço de sincronização de armazenamento.
- O tempo de vida de uma versão secundária do agente está associado à versão principal associada. Por exemplo, quando a versão do agente 3,0 é lançada, as versões 2 do agente. \* serão todas definidas para expirar em conjunto.

> [!Note]
> A instalação de uma versão do agente com um aviso de expiração exibirá um aviso, mas terá sucesso. A tentativa de instalar ou conectar-se a uma versão de agente expirada não tem suporte e será bloqueada.
