---
title: Utilizar o Azure AD Connect Health com sincronização | Microsoft Docs
description: Esta é a página do Azure AD Connect Health que irá discutir como monitorizar a sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e803614a02e76d179579a2258abd563b5c58e63a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016988"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Monitorizar a sincronização do Azure AD Connect com o Azure AD Connect Health
A seguinte documentação é específica para monitorizar a sincronização do Azure AD Connect (Sync) com o Azure AD Connect Health.  Para informações sobre como monitorizar o AD FS com o Azure AD Connect Health consulte [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md). Adicionalmente, para informações sobre como monitorizar os Serviços de Domínio do Active Directory com o Azure AD Connect Health consulte [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md).

![Screenshot da página Azure AD Connect Health for Sync.](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertas para o Azure AD Connect Health para sincronização
A secção Alertas do Azure AD Connect Health para sincronização apresenta-lhe a lista de alertas ativos. Cada alerta inclui informações relevantes, os passos de resolução e ligações para a documentação relacionada. Ao selecionar um alerta ativo ou resolvido, verá um novo painel com informações adicionais, bem como passos que pode tomar para resolver o alerta e ligações para documentação adicional. Pode também visualizar dados históricos de alertas que foram resolvidos no passado.

Ao selecionar um alerta, receberá informações adicionais, bem como passos que pode tomar para resolver o alerta e ligações para documentação adicional.

![Erro de sincronização do Azure AD Connect](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Avaliação Limitada de Alertas
Se o Azure AD Connect NÃO está a utilizar a configuração predefinida (por exemplo, se a Filtragem de Atributos é alterado da configuração predefinida para uma configuração personalizada), então o agente do Azure AD Connect Health não irá carregar os eventos de erro relacionados com o Azure AD Connect.

Isto limita a avaliação de alertas pelo serviço. Verá uma faixa que indica esta condição no Portal do Azure, no seu serviço.

![Screenshot do banner de alerta que diz que a avaliação de alerta é limitada. Atualize as suas definições para ativar todos os alertas.](./media/how-to-connect-health-sync/banner.png)

Pode alterar esta situação clicando em “Definições” e permitindo que o agente do Azure AD Connect Health carregue todos os registos de erros.

![Screenshot da opção Definições chamada e a secção Definições com a opção Guardar e a opção ON chamada.](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>Informações de Sincronização
Os administradores querem frequentemente obter informações sobre o tempo que demora a sincronizar alterações ao Azure AD e a quantidade de alterações executadas. Esta funcionalidade fornece uma forma fácil de visualizar estes dados através dos gráficos abaixo:   

* Latência de operações de sincronização
* Tendência de Alterações de Objetos

### <a name="sync-latency"></a>Latência de sincronização
Esta funcionalidade fornece uma tendência gráfica de latência das operações de sincronização (importar, exportar, etc.) para os conectores.  Proporciona uma forma rápida e fácil de entender não apenas a latência das suas operações (maior, se tiver um grande conjunto de alterações a ocorrerem), mas também uma forma de detetar anomalias na latência que possam exigir investigação adicional.

![Screenshot do run profile Latncy do gráfico de 3 dias passados.](./media/how-to-connect-health-sync/synclatency02.png)

Por predefinição, é apresentada apenas a latência da operação de 'Exportação' para o conector do Azure AD.  Para ver mais operações no conector ou para ver operações a partir de outros conectores, clique com o botão direito do rato no gráfico, selecione Editar Gráfico ou clique no botão "Editar Gráfico de Latência" e escolha a operação e conectores específicos.

### <a name="sync-object-changes"></a>Sincronização das Alterações de Objetos
Esta funcionalidade fornece uma tendência gráfica do número de alterações que estão a ser avaliadas e exportadas para o Azure AD.  É difícil, atualmente, tentar recolher estas informações a partir de registos de sincronização.  O gráfico fornece-lhe, não apenas uma forma mais simples de monitorizar, o número de alterações que estão a ocorrer no seu ambiente, mas também um amostra visual das falhas que estão a ocorrer.

![Screenshot das Estatísticas de Exportação para Azure AD dos últimos 3 dias gráfico.](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>Relatório de erro de sincronização de nível de objeto
Esta funcionalidade fornece um relatório sobre erros de sincronização que podem ocorrer quando os dados de identidade são sincronizados entre o Windows Server AD e o Azure AD com o Azure AD Connect.

* O relatório abrange os erros registados pelo cliente de sincronização (Azure AD Connect versão 1.1.281.0 ou superior)
* Inclui os erros que ocorreram na última operação de sincronização no motor de sincronização. ("Exportar" no Azure AD Connector.)
* O agente de sincronização do Azure AD Connect Health tem de ter conectividade de saída para os pontos finais necessários para o relatório incluir os dados mais recentes.
* O relatório é **atualizado após cada 30 minutos** usando os dados enviados pelo agente Azure AD Connect Health para sincronização. Fornece as seguintes capacidades-chave

  * Categorização de erros
  * Lista de objetos com o erro por categoria
  * Todos os dados sobre os erros num único local
  * Comparação lado a lado de objetos com o erro devido a um conflito
  * Descarregue o relatório de erro como UM CVS

### <a name="categorization-of-errors"></a>Categorização de Erros
O relatório categoriza os erros de sincronização existentes nas seguintes categorias:

| Categoria | Descrição |
| --- | --- |
| Atributo Duplicado |Erros quando o Azure AD Connect tenta criar ou atualizar objetos com valores duplicados de um ou mais atributos no Azure AD que têm de ser exclusivos num Inquilino, como proxyAddresses e UserPrincipalName. |
| Erro de Correspondência de Dados |Erros quando existe falha na correspondência de objetos que resulta em erros de sincronização. |
| Falha de Validação de Dados |Erros devido a dados inválidos, tais como carateres não suportados em atributos críticos, como UserPrincipalName, erros de formatação que falham a validação antes de serem escritos no Azure AD. |
| Alteração do Domínio Federado | Erros quando as contas utilizam um domínio federado diferente. |
| Atributo Grande |Erros quando um ou mais atributos são maiores do que o tamanho, o comprimento ou a contagem permitida. |
| Outros |Todos os outros erros que não se ajustam às categorias acima. Com base em feedback, esta categoria será dividida em sub-categorias. |

![Resumo do Relatório de Erros de Sincronização](./media/how-to-connect-health-sync/errorreport01.png)
![Categorias do Relatório de Erros de Sincronização](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Lista de objetos com o erro por categoria
Aprofundar cada categoria irá fornecer a lista de objetos com o erro nessa categoria.
![Lista de Relatórios de Erros de Sincronização](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>Detalhes do Erro
Os dados seguintes estão disponíveis na vista detalhada de cada erro

* Atributo em conflito destacado
* Identificadores para o *Objeto do AD* envolvido
* Identificadores para o *Objeto do Azure AD* envolvido (conforme aplicável)
* Descrição do erro e como corrigi-lo

![Detalhes do Relatório de Erros de Sincronização](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>Transferir o relatório de erros como CSV
Ao selecionar o botão "Exportação", pode transferir um ficheiro CSV com todos os detalhes sobre todos os erros.

### <a name="diagnose-and-remediate-sync-errors"></a>Diagnosticar e corrigir erros de sincronização 
Para cenários de erro de sincronização de atributo duplicado específico que envolvem uma atualização de Âncora de Origem do utilizador, pode corrigi-los diretamente no portal. Leia mais sobre [Diagnosticar e resolver erros de sincronização de atributo duplicado](how-to-connect-health-diagnose-sync-errors.md)

## <a name="related-links"></a>Ligações relacionadas
* [Erros de resolução de problemas durante a sincronização](tshoot-connect-sync-errors.md)
* [Resiliência de Atributos Duplicados](how-to-connect-syncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operações do Azure AD Connect Health](how-to-connect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Azure AD Connect Health Version History](reference-connect-health-version-history.md)