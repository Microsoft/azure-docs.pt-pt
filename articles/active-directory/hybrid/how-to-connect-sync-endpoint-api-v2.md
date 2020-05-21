---
title: Pré-visualização pública do ponto final do Azure AD Connect V2 Microsoft Docs
description: Este documento cobre atualizações para o Azure AD ligar pontos finais v2.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5501153a9c54d4509aa7dd948e03e011b30d79e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713816"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect sync V2 endpoint API (pré-visualização pública) 
A Microsoft implementou um novo ponto final (API) para o Azure AD Connect que melhora o desempenho das operações de serviço de sincronização para o Azure Ative Directory. Ao utilizar o novo ponto final do V2, irá experimentar ganhos de desempenho visíveis na exportação e importação para a Azure AD. Este novo ponto final suporta o seguinte:
    
 -  grupos de sincronização com até 250 k membros
 - ganhos de desempenho na exportação e importação para a AD Azure
 
> [!NOTE]
> Atualmente, o novo ponto final não tem um limite de tamanho de grupo configurado para grupos O365 que são reescritos. Isto pode ter um efeito no seu Diretório Ativo e nas tardios do ciclo de sincronização.  Recomenda-se aumentar gradualmente o tamanho do seu grupo.  


## <a name="pre-requisites"></a>Pré-requisitos  
Para utilizar o novo ponto final v2, terá de utilizar a [versão 1.5.30.0 do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) ou seguir os passos de implementação fornecidos abaixo para ativar o ponto final do V2 para o seu servidor Azure AD Connect.   

>[!NOTE]
>Atualmente, esta pré-visualização pública só está disponível na nuvem global do Azure e não está disponível para [nuvens nacionais.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)

### <a name="public-preview-limitations"></a>Limitações da pré-visualização pública  
Embora esta versão tenha sido submetida a testes extensivos, ainda pode encontrar problemas. Um dos objetivos deste lançamento público de pré-visualização é encontrar e corrigir tais problemas.  

>[!IMPORTANT]
> Embora seja fornecido suporte para esta versão de pré-visualização pública, a Microsoft pode nem sempre ser capaz de corrigir todos os problemas que poderá encontrar imediatamente. Por esta razão, recomenda-se que use o seu melhor julgamento antes de implementar esta versão no seu ambiente de produção. 

## <a name="deployment-guidance"></a>Orientação de implantação 
Terá de implantar a [versão 1.5.30.0 do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) ou posteriormente para utilizar o ponto final V2. Utilize o link fornecido para descarregar. 

Recomenda-se que siga o método de [migração](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration) do balanço para lançar o novo ponto final no seu ambiente. Isto proporcionará um plano de contingência claro no caso de ser necessário um grande retrocesso. O exemplo que se segue ilustra como uma migração de balanço pode ser usada neste cenário. Para obter mais informações sobre o método de implementação da migração do balanço, consulte o link fornecido. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Migração de balanço para implantação do ponto final v2
Os seguintes passos irão guiá-lo através da implantação do ponto final v2 utilizando o método de balanço.

1. Implante o ponto final V2 no servidor de preparação atual. Este servidor será conhecido como o **servidor V2** nos passos abaixo. O servidor ativo atual continuará a processar a carga de trabalho de produção utilizando o ponto final V1, que será chamado de **servidor V1** abaixo.
1. Valide que o **servidor V2** ainda está a processar importações como esperado. Nesta fase, os grandes grupos não serão aprovisionados para a AD Azure ou para a AD on-prem, mas poderá verificar se a atualização não resultou em qualquer outro impacto inesperado no processo de sincronização existente. 
2. Assim que a validação estiver concluída, altere o **servidor V2** para ser o servidor ativo e o **servidor V1** para ser o servidor de encenação. Neste momento, grandes grupos que estão no âmbito a sincronizar serão aprovisionados à Azure AD, bem como grandes grupos unificados o365 serão aprovisionados à AD, se a reprodução em grupo estiver ativada.
3. Valide que o **servidor V2** está a executar e processar grandes grupos com sucesso. Pode optar por ficar neste passo e monitorizar o processo de sincronização durante um período.
  >[!NOTE]
  > Se necessitar de fazer a transição de volta para a sua configuração anterior, pode realizar uma migração de balanço do **servidor V2** de volta para o **servidor V1**. Uma vez que o ponto final v1 não apoia grupos com mais de 50 k membros, qualquer grande grupo que tenha sido provisionado pela Azure AD Connect, em Azure AD ou on-prem AD, será posteriormente eliminado. 
4. Uma vez que esteja confiante em utilizar o ponto final V2, atualize o **servidor V1** para começar a utilizar o ponto final V2. 
 

## <a name="expectations-of-performance-impact"></a>Expectativas de impacto de desempenho  
Ao utilizar o ponto final V2, os ganhos de desempenho são uma função do número de grupos sincronizados, tamanho desses grupos, e do seu grupo churn (a atividade resultante da adição e remoção dos utilizadores como membros do grupo). A utilização do novo ponto final, sem aumentar o número, o tamanho ou a agitação dos grupos sincronizados, deverá resultar em tempos mais curtos para exportação e importação para a AD Azure. 
 
No entanto, os ganhos de desempenho podem ser anulados pelo processamento adicional necessário ao sincronizar grandes grupos. Pode acabar por aumentar o tempo de sincronização geral adicionando demasiados grandes grupos ao processo de sincronização.  

Para obter uma melhor compreensão de como a adição dos novos grupos terá impacto no seu desempenho de sincronização, recomenda-se que comece por sincronizar apenas alguns grandes grupos com menos de 100 k membros. Em seguida, pode aumentar o número e o tamanho dos grupos, trazendo mais deles ao seu alcance, através da filtragem de ou,atributos ou tamanho sem ar máximo. As melhorias de desempenho serão realizadas nas tarefas de exportação e importação do conector Azure AD, e não no conector AD no local. 

## <a name="deployment-step-by-step"></a>Implantação passo a passo 
As três fases seguintes são um exemplo aprofundado de implantação do novo ponto final V2.  Utilize as fases como uma orientação para a sua implantação.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fase 1 – instalar e validar o Azure AD Connect 
Recomenda-se que realize primeiro os passos para instalar ou atualizar para a [versão 1.5.30.0 do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) ou mais tarde e validar o processo de sincronização antes de passar para a segunda fase onde irá ativar o ponto final V2. No servidor Azure AD Connect: 


1. [Opcional] Pegue cópiade de dados 
2. Instale ou atualize para a [versão 1.5.30.0 do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) ou posteriormente.
3. Validar a instalação 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fase 2 – ativar o ponto final V2 
O próximo passo é ativar o ponto final v2. 

> [!NOTE]
> Depois de ter ativado o ponto final v2 para o seu servidor, poderá ver algumas melhorias de desempenho para a sua carga de trabalho existente. No entanto, ainda não conseguirá sincronizar grupos com mais 50k membros. 

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
 
Agora ativou o ponto final v2 para o seu servidor. Deva algum tempo para verificar se não existem resultados inesperados depois de ativar o ponto final V2 antes de passar para a fase seguinte, onde aumentará o limite de tamanho do grupo. 
>[!NOTE]
>Os caminhos de ficheiros/módulos podem utilizar uma letra de unidade diferente, dependendo da trajetória de instalação fornecida durante a instalação do Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fase 3 – aumentar o limite de adesão ao grupo 
Depois de verificar que o serviço está a decorrer sem resultados inesperados, pode continuar a elevar o limite de adesão ao grupo. Recomenda-se primeiro elevar o limite de adesão para um valor ligeiramente superior, por exemplo. 75K membros, para ver os grupos maiores sincronizando-se com AD Azure. Uma vez satisfeito com os resultados, pode aumentar ainda mais o limite de membro.  

O limite máximo é de 250 k por grupo. 

Podem ser utilizados os seguintes passos para aumentar o limite de adesão:  

1. Open Azure AD Synchronization Rules Editor 
2. No editor, escolha **Outbound** for Direction 
3. Clique na regra de sincronização **out to AAD – Group Join** 
4. Clique na regra de sincronização do botão **Editar** ![](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Clique no botão **Sim** para desativar a regra predefinida e criar uma cópia editável.
 ![Editar regra de sincronização](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. Na janela pop-up na página **Descrição,** defina a precedência para um valor disponível entre 1 e 99 ![ Regra de sincronização de edição](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Na página **Transformações,** atualize o valor **fonte** para a transformação do **membro,** substituindo '50000' por um valor entre 50001 e 250000. Esta substituição aumentará o tamanho máximo de adesão de grupos que irão sincronizar com a Azure AD. Sugerimos começar com uma série de 100 k, para entender o impacto que sincronizar grandes grupos terá no seu desempenho de sincronização. 
 
 **Exemplo** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Editar regra de sincronização](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Clicar em Guardar 
10. Aviso de administração aberta PowerShell 
11. Reativar o Programador de Sincronização 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Se o Azure AD Connect Health não estiver ativado, altere as definições de registo do evento de aplicação do Windows para arquivar os registos, em vez de os sobrepor. Os registos podem ser usados para ajudar em futuros esforços de resolução de problemas. 

>[!NOTE]
> Depois de ativar o novo ponto final, poderá ver erros adicionais de exportação no conector AAD com o nome "dn-atributos-falha". Haverá uma entrada correspondente de registo de eventos para cada erro com o id 6949, . Os erros são informáveis e não indicam um problema com a sua instalação, mas sim que o processo de sincronização não poderia adicionar certos membros a um grupo em Azure AD porque o objeto membro em si não estava sincronizado com a AD Azure. 

O novo código final V2 lida com alguns tipos de erros de exportação ligeiramente diferentes do que o código V1 fez.  Pode ver mais mensagens de erro informativas quando utilizar o ponto final V2. 

>[!NOTE]
> Ao atualizar o Azure AD Connect, certifique-se de que os passos na Fase 2 são reexecutados, uma vez que as alterações não são preservadas através do processo de atualização. 

Durante os aumentos subsequentes do limite de membro do grupo na regra de sincronização **out to AAD – Group Join,** não é necessária uma sincronização completa, para que possa optar por suprimir a sincronização completa executando o seguinte comando no PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Se tiver grupos unificados O365 com mais de 50k membros, os grupos serão lidos no Azure AD Connect, e se a reprodução em grupo estiver ativada, serão escritas para o seu ad. 

## <a name="rollback"></a>Recuo 
Se tiver ativado o ponto final v2 e precisar de recuar, siga estes passos: 

1. No servidor Azure AD Connect: a. [Opcional] Pegue cópiade de dados 
2. Abra um aviso de administração PowerShell:
3. Desative o programador de sincronização depois de verificar se não estão a decorrer operações de sincronização
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Mude para o ponto final V1 * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Open Azure AD Synchronization Rules Editor 
6. Eliminar a cópia editável da regra de sincronização **out to AAD – Group Join** 
7. Ativar a cópia predefinida da regra de sincronização **out to AAD – Group Join** 
8. Abra um aviso de administração PowerShell 
9. Reativar o Programador de Sincronização 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Ao mudar dos pontos finais V2 para V1, os grupos sincronizados com mais de 50k membros serão eliminados após a execução de uma sincronização completa, para ambos os grupos AD aprovisionados para grupos Azure AD e O365 unificados aprovisionados em AD. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes  
**P: Um cliente pode usar esta funcionalidade em produção?**  
</br>Sim, isto pode ser usado em ambientes de produção, com a ressalva como mencionado anteriormente.
 
**P: Quem pode o cliente contactar quando as coisas correm mal?**  
</br>Se precisar de apoio ao utilizar esta funcionalidade, deve abrir um caso de suporte. 
 
**P: Posso esperar atualizações frequentes para a pré-visualização pública?**  
</br>Há um grau limitado de mudanças em curso durante uma Pré-visualização pública.Deve avaliar este risco ao implementar funcionalidades de Pré-visualização Pública em produção.  
 
**P: Tempo para o próximo marco?**  
</br>As capacidades de pré-visualização pública podem ser retiradas e possivelmente redesenhadas antes de atingirem mais marcos.  
 
## <a name="next-steps"></a>Próximos passos

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
