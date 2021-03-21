---
title: Azure AD Connect Sync V2 | Microsoft Docs
description: Este documento cobre atualizações à Azure AD connect sync v2 pontos finais API.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ecfd277f2cc86102d59b201e7b43fa8519bdd3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937610"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API de ponto final do Azure AD Connect sync V2 
A Microsoft implementou um novo ponto final (API) para o Azure AD Connect que melhora o desempenho das operações de serviço de sincronização para o Azure Ative Directory. Utilizando o novo ponto final V2, irá experimentar ganhos de desempenho notáveis na exportação e importação para a Azure AD. Este novo ponto final suporta o seguinte:
    
 - syncing grupos com até 250k membros
 - ganhos de desempenho na exportação e importação para a Azure AD
 
> [!NOTE]
> Atualmente, o novo ponto final não tem um limite de tamanho de grupo configurado para os grupos Microsoft 365 que estão reensitados. Isto pode ter um efeito no seu Diretório Ativo e latências de ciclo sincronizado. Recomenda-se aumentar gradualmente os tamanhos do grupo.  

## <a name="prerequisites"></a>Pré-requisitos  
Para utilizar o novo ponto final V2, terá de utilizar a [versão 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) ou posterior do Azure AD Connect e seguir os passos de implementação abaixo fornecidos para ativar o ponto final V2 para o seu servidor Azure AD Connect.   

## <a name="deployment-guidance"></a>Orientação de implantação 
Terá de implantar a [versão 1.5.30.0 do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) ou mais tarde para utilizar o ponto final V2. Utilize o link fornecido para descarregar. 

Recomenda-se que siga o método [de migração do baloiço](./how-to-upgrade-previous-version.md#swing-migration) para lançar o novo ponto final no seu ambiente. Isto proporcionará um plano de contingência claro no caso de ser necessário um grande revés. O exemplo a seguir ilustra como uma migração de balanço pode ser usada neste cenário. Para obter mais informações sobre o método de implementação da migração do balanço, consulte o link fornecido. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Migração de balanço para implantação do ponto final V2
Os seguintes passos irão guiá-lo através da implantação do ponto final v2 utilizando o método de balanço.

1. Insiu o ponto de terminação V2 no servidor de paragem atual. Este servidor será conhecido como o **servidor V2** nos degraus abaixo. O servidor ativo atual continuará a processar a carga de trabalho de produção utilizando o ponto final V1, que será chamado de **servidor V1** abaixo.
1. Validar que o **servidor V2** ainda está a processar as importações como esperado. Nesta fase, grandes grupos não serão a provisionados para Azure AD ou AD on-prem, mas você será capaz de verificar que a atualização não resultou em qualquer outro impacto inesperado para o processo de sincronização existente. 
2. Uma vez concluída a validação, altere o **servidor V2** para ser o servidor ativo e o **servidor V1** para ser o servidor de paragem. Neste momento, grandes grupos que estão em âmbito de sincronização serão a provisionados ao Azure AD, bem como aos grandes grupos unificados da Microsoft 365 serão a provisionados para AD, se a redução do grupo estiver ativada.
3. Validar que o **servidor V2** está a executar e processar grandes grupos com sucesso. Pode optar por ficar neste passo e monitorizar o processo de sincronização durante um período.
  >[!NOTE]
  > Se precisar de voltar à sua configuração anterior, pode efetuar uma migração do **servidor V2** para o **servidor V1**. Uma vez que o ponto final V1 não apoia grupos com mais de 50mil membros, qualquer grupo grande que tenha sido a provisionado pelo Azure AD Connect, em Azure AD ou em AD on-prem, será posteriormente eliminado. 
4. Uma vez confiante na utilização do ponto final V2, atualize o **servidor V1** para começar a utilizar o ponto final V2. 
 

## <a name="expectations-of-performance-impact"></a>Expectativas de impacto no desempenho  
Ao utilizar o ponto final V2, os ganhos de desempenho são uma função do número de grupos sincronizados, tamanho desses grupos e do seu grupo (a atividade resultante da adição e remoção dos utilizadores como membros do grupo). A utilização do novo ponto final, sem aumentar o número, o tamanho ou a agitação dos grupos sincronizados, deverá resultar em tempos mais curtos para exportação e importação para a Azure AD. 
 
No entanto, os ganhos de desempenho podem ser negados pelo processamento adicional necessário ao sincronizar grandes grupos. Pode acabar por aumentar o tempo de sincronização global adicionando muitos grupos grandes ao processo de sincronização.  

Para obter uma melhor compreensão de como a adição dos novos grupos irá afetar o seu desempenho sincronizado, recomenda-se que comece por sincronizar apenas alguns grandes grupos com menos de 100 mil membros. Em seguida, pode aumentar o número e o tamanho dos grupos, trazendo mais deles no seu âmbito, através da ou, atribuição ou filtragem do tamanho máximo do grupo. As melhorias de desempenho serão realizadas nas tarefas de exportação e importação do conector Azure AD, e não no conector AD no local. 

## <a name="deployment-step-by-step"></a>Implantação passo a passo 
As três fases seguintes são um exemplo aprofundado da implantação do novo ponto final V2.  Utilize as fases como orientação para a sua implantação.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fase 1 – instalar e validar a Azure AD Connect 
Recomenda-se que execute primeiro os passos para instalar ou atualizar para a [versão 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) ou posterior do Azure AD Connect e validar o processo de sincronização antes de ir para a segunda fase onde irá ativar o ponto de final V2. No servidor Azure AD Connect: 


1. [Opcional] Faça backup de base de dados 
2. Instale ou atualize a [versão 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) ou posterior do Azure AD Connect.
3. Validar a instalação 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fase 2 – ativar o ponto final V2 
O próximo passo é ativar o ponto final V2. 

> [!NOTE]
> Depois de ter ativado o ponto final V2 para o seu servidor, poderá ver algumas melhorias de desempenho para a carga de trabalho existente. No entanto, ainda não será capaz de sincronizar grupos com mais membros de 50K. 

Para mudar para o ponto final V2, utilize os seguintes passos: 

1. Abra um pedido powerShell como administrador. 
2. Desative o programador de sincronização depois de verificar se não estão a decorrer operações de sincronização: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importar o novo módulo: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Mude para o ponto final v2:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Ativou agora o ponto final V2 para o seu servidor. Dedem um pouco para verificar se não existem resultados inesperados depois de permitir o ponto final V2 antes de passar para a fase seguinte onde irá aumentar o limite de tamanho do grupo. 
>[!NOTE]
>Os caminhos de ficheiro/módulo podem utilizar uma letra de unidade diferente, dependendo do caminho de instalação fornecido ao instalar o Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fase 3 – aumentar o limite de adesão ao grupo 
Depois de verificar que o serviço está a funcionar sem resultados inesperados, pode proceder ao aumento do limite de adesão ao grupo. Recomenda-se primeiro elevar o limite de adesão para um valor ligeiramente mais elevado, por exemplo. Membros 75K, para ver os grupos maiores sincronizando a Azure AD. Uma vez satisfeito com os resultados, pode aumentar ainda mais o limite de membro.  

O limite máximo é de 250 mil membros por grupo. 

Podem ser utilizados os seguintes passos para aumentar o limite de adesão:  

1. Open Azure AD Synchronization Rules Editor 
2. No editor, escolha **Outbound** for Direction 
3. Clique na **regra out to AAD – Grupo Junte-se à** regra de sincronização 
4. Clique no botão **Editar** ![ Screenshot que mostra as "Ver e gerir as suas regras de sincronização" com "out to AAD - Group Join" selecionado.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Clique no botão **Sim** para desativar a regra predefinida e criar uma cópia editável.
 ![Screenshot que mostra a janela "Editar confirmação de regras reservadas" com o botão "Sim" selecionado.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. Na janela pop-up da página **Descrição,** desloque a precedência para um valor disponível entre 1 e 99 ![ Screenshot que mostra a janela "Editar a regra de sincronização de saída" com "Precedência" realçada.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Na página **Transformações,** atualize o valor **Fonte** para a transformação dos **membros,** substituindo '50000' por um valor entre 50001 e 250000. Esta substituição aumentará o tamanho máximo de membros de grupos que irão sincronizar com a Azure AD. Sugerimos começar com um número de 100k, para entender o impacto que a sincronização de grandes grupos terá no seu desempenho sincronizado. 
 
 **Exemplo** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Editar regra de sincronização](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Clicar em Guardar 
10. Abertura admin PowerShell 
11. Re-ativar o Programador de Sincronização 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Se o Azure AD Connect Health não estiver ativado, altere as definições de registo de eventos de aplicação do Windows para arquivar os registos, em vez de os sobrepor. Os registos podem ser utilizados para ajudar em futuros esforços de resolução de problemas. 

>[!NOTE]
> Depois de ativar o novo ponto final, poderá ver erros de exportação adicionais no conector AAD com o nome "dn-atributos-falha". Haverá uma entrada de registo de evento correspondente para cada erro com o id 6949. Os erros são informativos e não indicam um problema com a sua instalação, mas sim que o processo de sincronização não poderia adicionar certos membros a um grupo em Azure AD porque o próprio objeto membro não estava sincronizado com a Azure AD. 

O novo código de ponta V2 trata alguns tipos de erros de exportação ligeiramente diferentes do que o código V1 fez.  Pode ver mais mensagens de erro informativas quando utilizar o ponto de terminação V2. 

>[!NOTE]
> Ao atualizar o Azure AD Connect, certifique-se de que os passos da Fase 2 são reexecutados, uma vez que as alterações não são preservadas através do processo de atualização. 

Durante os aumentos subsequentes ao limite de membro do grupo na regra **out to AAD – Group Join** sync, não é necessária uma sincronização completa, para que possa optar por suprimir toda a sincronização executando o seguinte comando em PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Se tiver o Microsoft 365 grupos unificados que tenham mais de 50 mil membros, os grupos serão lidos no Azure AD Connect, e se a gravação do grupo estiver ativada, serão escritas para o seu AD no local. 

## <a name="rollback"></a>Reversão 
Se tiver ativado o ponto final v2 e precisar de ser revoado, siga estes passos: 

1. No servidor Azure AD Connect: a. [Opcional] Faça backup de base de dados 
2. Abra um aviso de administração PowerShell:
3. Desative o programador de sincronização depois de verificar se não estão a decorrer operações de sincronização
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Mude para o ponto final V1 * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Open Azure AD Synchronization Rules Editor 
6. Eliminar a cópia editável da **Regra out to AAD – Grupo Juntar-se** à sincronização 
7. Ativar a cópia padrão da **regra out to AAD – Group Join** sync 
8. Abra um pedido de administração PowerShell 
9. Re-ativar o Programador de Sincronização 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Ao mudar de ponto final V2 para V1, grupos sincronizados com mais de 50k membros serão eliminados após a execução de uma sincronização completa, tanto para os grupos AD a provisionados a Azure AD como para os grupos unificados microsoft 365 a provisionados a AD. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes  
 
**Quando é que o novo ponto final se tornará o padrão para upgrades e novas instalações?**  
</br>Estamos a planear um novo lançamento do AADConnect para download em fevereiro de 2021. Esta versão utilizará o ponto de final V2 por predefinição e permitirá a sincronização de grupos maiores do que 50K sem qualquer configuração adicional. Esta versão será posteriormente publicada para atualização automática para servidores elegíveis.
 
## <a name="next-steps"></a>Passos seguintes

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
