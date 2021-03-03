---
title: Controlos de aplicações adaptáveis no Centro de Segurança do Azure
description: Este documento ajuda-o a utilizar o controlo de aplicações adaptativas no Azure Security Center para permitir a lista de aplicações em funcionamento em máquinas Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: b18b498f250e9ff62180ba21c3bcbda562b13b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735867"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Utilize controlos de aplicação adaptativos para reduzir as superfícies de ataque das suas máquinas

Conheça os benefícios dos controlos de aplicação adaptativos do Azure Security Center e como pode melhorar a sua segurança com esta funcionalidade inteligente e orientada para os dados.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Quais são os controlos de aplicação adaptativos do Security Center?

Os controlos de aplicação adaptativa são uma solução inteligente e automatizada para definir listas de aplicações conhecidas e seguras para as suas máquinas. 

Muitas vezes, as organizações têm coleções de máquinas que rotineiramente executam os mesmos processos. O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e criar uma lista do software conhecido e seguro. As listas de permitir são baseadas nas suas cargas de trabalho específicas do Azure, e pode personalizar ainda mais as recomendações usando as instruções abaixo.

Quando tiver ativado e configurado controlos de aplicação adaptativa, receberá alertas de segurança se qualquer aplicação correr além das que definiu como seguras.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Quais são os benefícios dos controlos de aplicações adaptativos?

Ao definir listas de aplicações conhecidas e gerar alertas quando qualquer outra coisa é executada, pode alcançar múltiplos objetivos de endurecimento:

- Identifique malware potencial, mesmo qualquer que possa ser perdido por soluções antimalware
- Melhorar o cumprimento das políticas de segurança locais que ditam o uso de apenas software licenciado
- Evite executar aplicações antigas ou não apoiadas
- Evite software específico que seja proibido pela sua organização
- Aumentar a supervisão das apps que acedem a dados sensíveis

Atualmente, não existem opções de aplicação da lei. Os controlos de aplicação adaptativa destinam-se a fornecer alertas de segurança se qualquer aplicação correr além das que definiu como seguras.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|Requer [Azure Defender para servidores](defender-for-servers-introduction.md)|
|Máquinas suportadas:|![Sim ](./media/icons/yes-icon.png) Azure e máquinas não-Azure que executam Windows e Linux<br>![Sim ](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml) máquinas|
|Funções e permissões necessárias:|**As** funções de Leitor de Segurança e **Leitor** podem ver grupos e listas de aplicações conhecidas e seguras<br>**As** funções de Administrador de Colaborador e **Segurança** podem editar grupos e listas de aplicações conhecidas e seguras|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Permitir controlos de aplicação num grupo de máquinas

Se o Security Center tiver identificado grupos de máquinas nas suas subscrições que executam consistentemente um conjunto de aplicações semelhantes, será solicitado com a seguinte recomendação: Os **controlos de aplicações adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas**.

Selecione a recomendação ou abra a página de controlos de aplicações adaptativas para ver a lista de aplicações e grupos de máquinas conhecidos sugeridos.

1. Abra o painel de instrumentos Azure Defender e a partir da área de proteção avançada, selecione **comandos de aplicação adaptáveis**.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Abertura de controlos de aplicação adaptativa a partir do Painel Azure" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    A página **de controlos de aplicação adaptativa** abre com os seus VMs agrupados nos seguintes separadores:

    - **Configurado** - Grupos de máquinas que já têm uma lista de aplicações definida. Para cada grupo, o separador configurado mostra:
        - o número de máquinas no grupo
        - alertas recentes

    - **Recomendado** - Grupos de máquinas que executam consistentemente as mesmas aplicações, e não têm uma lista de permitis configuradas. Recomendamos que permita controlos de aplicação adaptativos para estes grupos.
    
      > [!TIP]
      > Se vir um nome de grupo com o prefixo "REVIEWGROUP", contém máquinas com uma lista parcialmente consistente de aplicações. O Security Center não consegue ver um padrão, mas recomenda a revisão deste grupo para ver _se_ pode definir manualmente algumas regras de controlo de aplicações adaptativas, conforme descrito na [Edição da regra de controlos de aplicação adaptativa de um grupo.](#edit-a-groups-adaptive-application-controls-rule)
      >
      > Também pode mover máquinas deste grupo para outros grupos, conforme descrito no [Move uma máquina de um grupo para outro](#move-a-machine-from-one-group-to-another).

    - **Sem recomendação** - Máquinas sem uma lista de aplicações definidas, e que não suportam a funcionalidade. A sua máquina pode estar neste separador pelas seguintes razões:
      - Falta um agente do Log Analytics.
      - O agente do Log Analytics não está a enviar eventos.
      - É uma máquina Windows com uma política [appLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) pré-existente, ativada por um GPO ou por uma política de segurança local

      > [!TIP]
      > O Centro de Segurança precisa de pelo menos duas semanas de dados para definir as recomendações únicas por grupo de máquinas. As máquinas que foram criadas recentemente, ou que pertencem a subscrições que só recentemente foram ativadas com o Azure Defender, aparecerão no separador **No recommendation.**


1. Abra a lingueta **recomendada.** Aparecem os grupos de máquinas com listas de admissão recomendadas.

   ![Separador recomendado](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Selecione um grupo. 

1. Para configurar a sua nova regra, reveja as várias secções desta página de regras de controlo de **aplicações Configure** e os conteúdos, que serão exclusivos deste grupo específico de máquinas:

   ![Configurar uma nova regra](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Selecione máquinas** - Por predefinição, todas as máquinas do grupo identificado são selecionadas. Desescolh-lhes qualquer um para os retirar desta regra.
   
   1. **Aplicações recomendadas** - Reveja esta lista de aplicações que são comuns às máquinas dentro deste grupo, e recomenda-se que sejam permitidas a funcionar.
   
   1. **Mais aplicações** - Reveja esta lista de aplicações que são vistas com menos frequência nas máquinas dentro deste grupo, ou que são conhecidas por serem exploráveis. Um ícone de aviso indica que uma aplicação específica pode ser usada por um intruso para contornar uma lista de admissões. Recomendamos que reveja cuidadosamente estas aplicações.

      > [!TIP]
      > Ambas as listas de aplicações incluem a opção de restringir uma aplicação específica a determinados utilizadores. Adotar o princípio do menor privilégio sempre que possível.
      > 
      > As aplicações são definidas pelos seus editores, se uma aplicação não tiver informações de editores (não assinada), uma regra de caminho é criada para o caminho completo da aplicação específica.

   1. Para aplicar a regra, **selecione Audit**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Editar a regra de controlos de aplicação adaptativa de um grupo

Pode decidir editar a lista de autorizações para um grupo de máquinas devido a alterações conhecidas na sua organização. 

Para editar as regras para um grupo de máquinas:

1. Abra o painel de instrumentos Azure Defender e a partir da área de proteção avançada, selecione **comandos de aplicação adaptáveis**.

1. A partir do separador **Configurado,** selecione o grupo com a regra que pretende editar.

1. Reveja as várias secções da página de regras de controlo de **aplicações Configure,** tal como descrito no [Enable adaptive application controls on a group of machines](#enable-application-controls-on-a-group-of-machines).

1. Opcionalmente, adicione uma ou mais regras personalizadas:

   1. **Selecione Adicionar regra**.

      ![Adicione uma regra personalizada](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Se estiver a definir um caminho seguro conhecido, altere o **tipo regra** para 'Caminho' e entre num único caminho. Pode incluir wildcards no caminho.
   
      > [!TIP]
      > Alguns cenários para os quais os wildcards num caminho podem ser úteis:
      > 
      > * Utilizar um wildcard no final de um caminho para permitir que todos os executáveis dentro desta pasta e sub-pastas.
      > * Utilizar um wildcard no meio de um caminho para permitir um nome executável conhecido com um nome de pasta em mudança (por exemplo, pastas pessoais do utilizador contendo nomes de pastas geradas automaticamente, ou seja, automaticamente gerados).
  
   1. Defina os utilizadores autorizados e os tipos de ficheiros protegidos.

   1. Quando terminar de definir a regra, **selecione Add**.

1. Para aplicar as alterações, **selecione Guardar**.


## <a name="review-and-edit-a-groups-settings"></a>Reveja e edite as definições de um grupo

1. Para ver os detalhes e configurações do seu grupo, selecione **as definições do Grupo**

    Este painel mostra o nome do grupo (que pode ser modificado), o tipo de SO, a localização e outros detalhes relevantes.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="A página de definições de grupo para controlos de aplicações adaptativas" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Opcionalmente, modifique o nome do grupo ou os modos de proteção do tipo de ficheiro.

1. **Selecione Aplicar** e **Guardar**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Responder à recomendação "Allowlist na sua política de controlo de aplicações adaptativas deve ser atualizada"

Verá esta recomendação quando a aprendizagem automática do Centro de Segurança identificar um comportamento potencialmente legítimo que não tenha sido permitido anteriormente. A recomendação sugere novas regras para as suas definições existentes para reduzir o número de alertas falsos positivos.

Para remediar as questões:

1. Na página de recomendações, selecione **as regras Allowlist na sua política de controlo de aplicações adaptativas deve ser atualizada** recomendação para ver grupos com comportamentos potencialmente legítimos recentemente identificados.

1. Selecione o grupo com a regra que pretende editar.

1. Reveja as várias secções da página de regras de controlo de **aplicações Configure,** tal como descrito no [Enable adaptive application controls on a group of machines](#enable-application-controls-on-a-group-of-machines).

1. Para aplicar as alterações, **selecione Audit**.




## <a name="audit-alerts-and-violations"></a>Alertas de auditoria e violações

1. Abra o painel de instrumentos Azure Defender e a partir da área de proteção avançada, selecione **comandos de aplicação adaptáveis**.

1. Para ver grupos com máquinas que têm alertas recentes, reveja os grupos listados no separador **Configurado.**

1. Para investigar mais, selecione um grupo.

   ![Alertas recentes](./media/security-center-adaptive-application/recent-alerts.png)

1. Para mais detalhes e a lista de máquinas afetadas, selecione um alerta.



## <a name="move-a-machine-from-one-group-to-another"></a>Mover uma máquina de um grupo para outro

Quando se desloca uma máquina de um grupo para outro, a política de controlo de aplicações aplicada a ela altera-se às definições do grupo para onde a transferiu. Também pode mover uma máquina de um grupo configurado para um grupo não configurado, eliminando assim quaisquer regras de controlo de aplicação que foram aplicadas à máquina.

1. Abra o painel de instrumentos Azure Defender e a partir da área de proteção avançada, selecione **comandos de aplicação adaptáveis**.

1. A partir da página de controlos de **aplicação Adaptive,** a partir do separador **Configurado,** selecione o grupo que contém a máquina a ser movida.

1. Abra a lista de **máquinas configuradas.**

1. Abra o menu da máquina a partir de três pontos no final da linha e selecione **Move**. Abre-se a máquina Move para um painel de **grupo diferente.**

1. Selecione o grupo de destino e selecione **a máquina Move**.

1. Selecione **Guardar** para guardar as suas alterações.





## <a name="manage-application-controls-via-the-rest-api"></a>Gerir os controlos de aplicações através da API REST 

Para gerir os controlos de aplicação adaptativas, utilize a nossa API REST. 

A documentação relevante da API está disponível na [secção de Controlos de Aplicações Adaptativas dos docs API do Centro de Segurança.](/rest/api/securitycenter/adaptiveapplicationcontrols)

Algumas das funções que estão disponíveis na API REST:

* **A lista** recupera todas as recomendações do seu grupo e fornece um JSON com um objeto para cada grupo.

* **Obtenha** a recuperação do JSON com os dados de recomendação completos (isto é, lista de máquinas, regras de editor/caminho, e assim por diante).

* **Configurar** a sua regra (use o JSON que recuperou com **Get** como corpo para este pedido).
 
   > [!IMPORTANT]
   > A função **Put** espera menos parâmetros do que o JSON devolvido pelo comando Get contém.
   >
   > Remova as seguintes propriedades antes de utilizar o JSON no pedido Demissão: recomendaçãoStatus, configuraçãoStatus, problemas, localização e sourceSystem.


## <a name="faq---adaptive-application-controls"></a>FAQ - Controlos de aplicações adaptativos

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Existem opções para impor os controlos de aplicação?
Atualmente, não existem opções de aplicação da lei. Os controlos de aplicação adaptativa destinam-se a fornecer **alertas** de segurança se qualquer aplicação correr além das que definiu como seguras. Têm uma gama de benefícios[(Quais são os benefícios dos controlos de aplicação adaptativa?](#what-are-the-benefits-of-adaptive-application-controls)) e são extremamente personalizáveis como mostrado nesta página.

 

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a utilizar o controlo de aplicações adaptativas no Azure Security Center para definir as listas de aplicações em execução nas suas máquinas Azure e não-Azure. Para saber mais sobre algumas das outras funcionalidades de proteção da carga de trabalho em nuvem do Security Center, consulte:

* [Compreensão do acesso just-in-time (JIT) à VM](just-in-time-explained.md)
* [Assegurar os seus clusters Azure Kubernetes](defender-for-kubernetes-introduction.md)