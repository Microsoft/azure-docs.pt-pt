---
title: Usando regras de supressão de alertas para suprimir falsos positivos ou outros alertas de segurança indesejados no Centro de Segurança Azure.
description: Este artigo explica como usar as regras de supressão do Azure Security Center para esconder alertas de segurança indesejados.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 83ee59ca4353cd1af6c613e7624ee15f21b0a27e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650921"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>Suprimir alertas da proteção contra ameaças do Azure Security Center

Esta página explica como pode usar regras de supressão de alertas para suprimir falsos positivos ou outros alertas de segurança indesejados no Centro de Segurança Azure.

## <a name="availability"></a>Disponibilidade

- Estado de lançamento: **Pré-visualização**
- Funções necessárias: A administração de segurança e o proprietário podem criar/eliminar regras. O leitor de segurança e o leitor podem ver regras.
- Nuvens: Todos (Global, Nacional, Governo e Soberano)


## <a name="introduction-to-suppression-rules"></a>Introdução às regras de supressão

Os componentes de proteção contra ameaças do Azure Security Center detetam ameaças em qualquer área do seu ambiente e geram alertas de segurança.

Quando um único alerta não é interessante ou relevante, pode descartá-lo manualmente. Em alternativa, utilize a funcionalidade de regras de supressão para descartar automaticamente alertas semelhantes no futuro. Normalmente, usaria uma regra de supressão para:

- suprimir alertas que identificou como falsos positivos

- suprimir alertas que estão sendo desencadeados demasiadas vezes para ser útil

As suas regras de supressão definem os critérios para os quais os alertas devem ser automaticamente descartados.

> [!CAUTION]
> Suprimir alertas de segurança reduz a proteção contra ameaças do Centro de Segurança. Deve verificar cuidadosamente o impacto potencial de qualquer regra de supressão e monitorizá-la ao longo do tempo.

[![Página de alertas de segurança do Azure Security Center com opções de supressão de alertas](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>Criar uma regra de supressão

Há algumas maneiras de criar regras para suprimir alertas de segurança indesejados:

- Para suprimir alertas a nível do grupo de gestão, use a Política Azure

- Para suprimir alertas ao nível da subscrição, pode utilizar o portal Azure ou a API REST, conforme explicado abaixo.

As regras de supressão só podem descartar alertas que já tenham sido desencadeados nas subscrições selecionadas.

Para criar uma regra diretamente no portal Azure:

1. Da página de alertas de segurança do Security Center:

    - Localize o alerta específico que não pretende ver mais, e a partir do menu de elipse (...) para o alerta, selecione Regra de **supressão Criar:**

        [![**Criar a opção de supressão**](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - Ou, selecione o link de regras de **supressão** no topo da página, e a partir da página regras de supressão selecione Criar nova regra de **supressão:**

        ![Crie novo botão de regra de supressão**](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. No novo painel de regras de supressão, introduza os detalhes da sua nova regra.

    - A sua regra pode descartar o alerta sobre **todos os recursos** para que não receba nenhum alerta como este no futuro. 
    
    - A sua regra pode descartar o alerta **em critérios específicos** - quando se trata de um endereço IP específico, nome do processo, conta de utilizador, recurso Azure ou localização.

    > [!TIP]
    > Se abrir a nova página de regras a partir de um alerta específico, o alerta e a subscrição serão automaticamente configurados na sua nova regra. Se utilizou o novo link de regra de **supressão Criar,** as subscrições selecionadas corresponderão ao filtro atual no portal.

    [![Painel de criação de regras de supressão](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. Introduza detalhes da regra:

    - **Nome** - Um nome para a regra. Os nomes das regras devem começar com uma letra ou um número, estar entre 2 e 50 caracteres, e não conter outros símbolos que não sejam traços (-) ou sublinhados (_). 
    - **Estado** - Habilitado ou incapacitado.
    - **Razão** - Selecione uma das razões incorporadas ou 'outra' se não atender às suas necessidades.
    - **Data** de validade - Data e hora de fim para a regra. As regras podem funcionar até seis meses.

1. Opcionalmente, teste a regra usando o botão **Simulação** para ver quantos alertas teriam sido rejeitados se esta regra estivesse ativa.

1. Salve a regra. 

## <a name="editing-suppression-rules"></a>Regras de supressão de edição

Para editar as regras que criou, use a página de regras de supressão.

1. A partir da página de alertas de segurança do Security Center, selecione o link de regras de **supressão** no topo da página.

1. A página de regras de supressão abre a lista de todas as regras disponíveis de acordo com as subscrições atualmente selecionadas. 

    [![Lista de regras de supressão](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Para editar uma única regra, abra o menu de elipse (...) para a regra e **selecione Editar**.

1. Faça as alterações necessárias e selecione **Aplicar**. 

## <a name="deleting-suppression-rules"></a>Eliminação de regras de supressão

Para eliminar uma ou mais regras que criou, use a página de regras de supressão.

1. A partir da página de alertas de segurança do Security Center, selecione o link de regras de **supressão** no topo da página.

1. A página de regras de supressão abre a lista de todas as regras disponíveis de acordo com as subscrições atualmente selecionadas. 

1. Para eliminar uma única regra, abra o menu de elipse (...) para a regra e selecione **Eliminar**.

1. Para eliminar várias regras, selecione as caixas de verificação para que as regras sejam eliminadas e selecione **Eliminar**.

    ![Eliminação de uma ou mais regras de supressão](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>Alertas de visualização que foram suprimidos

Os alertas que correspondam às suas regras de supressão ativadas continuarão a ser gerados, mas o seu estado será posto **de fora.** Pode ver o estado no portal Azure ou no que tiver acesso aos seus alertas de segurança do Security Center. 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) não vai criar incidentes para alertas reprimidos. Para outros SIEMs, pode filtrar alertas suprimidos utilizando o estado dos alertas ('dispensado').

Utilize o filtro do Centro de Segurança para visualizar alertas que tenham sido rejeitados pelas suas regras.

* A partir da página de alertas de segurança do Security Center, abra as opções do filtro e **selecione Dispensado**.  

   [![Visualização de alertas dispensados](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>Usar a API para criar e gerir regras de supressão

Pode criar, visualizar ou eliminar regras de supressão de alerta através da API REST do Centro de Segurança. 

Os métodos http relevantes para as regras de supressão na API REST são:

- **PUT**: Para criar ou atualizar uma regra de supressão numa subscrição especificada.

- **GET:**

    - Para listar todas as regras configuradas para uma subscrição especificada. Este método devolve uma série de regras aplicáveis.

    - Para obter os detalhes de uma regra específica sobre uma subscrição especificada. Este método devolve uma regra de supressão.

    - Para simular o impacto de uma regra de supressão ainda na fase de conceção. Esta chamada identifica qual dos seus alertas existentes teria sido rejeitado se a regra estivesse ativa.

- **DELETE**: Elimina uma regra existente (mas não altera o estado dos alertas já rejeitados por ela).

Para mais detalhes e exemplos de utilização, consulte a documentação da [API](https://docs.microsoft.com/rest/api/securitycenter/). 


## <a name="next-steps"></a>Passos seguintes

Este artigo descrevia as regras de supressão no Centro de Segurança Azure que automaticamente descartam alertas indesejados.

Para obter mais informações sobre alertas de segurança no Centro de Segurança Azure, consulte as seguintes páginas:

- [Alertas de segurança e a cadeia de morte intencional](alerts-reference.md) - Um guia de referência para os alertas de segurança que pode ver no módulo de Proteção de Ameaças do Azure Security Center.
- [Proteção contra ameaças no Azure Security Center](threat-protection.md) - Uma descrição dos muitos aspetos do seu ambiente monitorizados pelo módulo de proteção contra ameaças do Azure Security Center.