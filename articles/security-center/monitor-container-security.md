---
title: Monitorização da segurança dos seus contentores no Centro de Segurança Azure
description: Saiba como verificar a postura de segurança dos seus contentores a partir do Centro de Segurança Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 5f558aaa367d156a8a4fa8633f113c6480236f5b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038619"
---
# <a name="monitor-the-security-of-your-containers"></a>Monitorize a segurança dos seus contentores

Esta página explica como utilizar as funcionalidades de segurança do contentor descritas no [artigo de Segurança](container-security.md) do Contentor na nossa secção de conceitos.


## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Digitalizar os registos de contentores baseados em ARM para vulnerabilidades 

1. Para permitir a verificação de vulnerabilidades das imagens do registo do seu contentor Azure:

    1. Certifique-se de que está no nível de preços padrão do Azure Security Center.

    1. A partir da página **de definições de &** de preços, ative o pacote opcional de registos de contentores para a sua subscrição: ![ Ativar o pacote de registos de contentores](media/monitor-container-security/enabling-container-registries-bundle.png)

        O Centro de Segurança está agora pronto para digitalizar imagens que são empurradas para o registo. 

        >[!NOTE]
        >Esta funcionalidade é carregada por imagem.


1. Para ativar a digitalização de uma imagem, empurre-a para o seu registo. 

    Quando a varredura termina (normalmente após aproximadamente 2 minutos, mas pode ser até 15 minutos), os resultados estão disponíveis como recomendações do Centro de Segurança como esta:

1. Para ver os resultados, vá à página **de Recomendações.** Se os problemas forem encontrados, verá a seguinte recomendação:

    ![Recomendação para remediar questões ](media/monitor-container-security/acr-finding.png)

1. Selecione a recomendação. 
    A página de detalhes da recomendação abre com informações adicionais. Esta informação inclui a lista de registos com imagens vulneráveis ("Recursos afetados") e as medidas de reparação. 

1. Selecione um registo específico para ver os repositórios dentro dele que têm repositórios vulneráveis.

    ![Selecione um registo](media/monitor-container-security/acr-finding-select-registry.png)

    A página de detalhes do registo abre com a lista de repositórios afetados.

1. Selecione um repositório específico para ver os repositórios dentro dele que têm imagens vulneráveis.

    ![Selecione um repositório](media/monitor-container-security/acr-finding-select-repository.png)

    A página de detalhes do repositório abre. Ele lista as imagens vulneráveis juntamente com uma avaliação da gravidade das descobertas.

1. Selecione uma imagem específica para ver as vulnerabilidades.

    ![Selecione imagens](media/monitor-container-security/acr-finding-select-image.png)

    Abre a lista de resultados para a imagem selecionada.

    ![Lista de resultados](media/monitor-container-security/acr-findings.png)

1. Para saber mais sobre uma descoberta, selecione a descoberta. 

    Os detalhes das descobertas abrem.-

    [![Conclusões detalham painel](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Este painel inclui uma descrição detalhada da questão e liga-se a recursos externos para ajudar a mitigar as ameaças.

1. Siga os passos na secção de reparação deste painel.

1. Quando tiver tomado as medidas necessárias para remediar a questão de segurança, substitua a imagem no seu registo:

    1. Empurre a imagem atualizada. Isto vai desencadear uma tomografia. 
    
    1. Consulte a página de recomendações para obter a recomendação "Vulnerabilidades nas imagens do Registo de Contentores Azure devem ser remediadas". 
    
        Se a recomendação ainda aparecer e a imagem que manuseou ainda aparecer na lista de imagens vulneráveis, verifique novamente os passos de reparação.

    1. Quando tiver a certeza de que a imagem atualizada foi empurrada, digitalizada e já não aparece na recomendação, elimine a imagem vulnerável "antiga" do seu registo.


## <a name="hardening-your-containers-docker-hosts"></a>Endurecendo os anfitriões do Docker dos seus contentores

O Security Center monitoriza constantemente a configuração dos seus anfitriões Docker e gera recomendações de segurança que refletem os padrões da indústria.

Para ver as recomendações de segurança do Azure Security Center para os anfitriões do Docker dos seus contentores:

1. A partir da barra de navegação do Security Center, abra **as aplicações de & Compute** e selecione o **separador Contentores.**

1. Opcionalmente, filtre a lista dos recursos do seu contentor para os anfitriões dos contentores.

    ![Filtro de recursos de contentores](media/monitor-container-security/container-resources-filter.png)

1. A partir da lista das máquinas hospedeiras do seu contentor, selecione uma para investigar mais.

    ![Recomendações do hospedeiro de contentores](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    A **página de informações do anfitrião do Contentor** abre com detalhes do anfitrião e uma lista de recomendações.

1. A partir da lista de recomendações, selecione uma recomendação para investigar mais aprofundadamente.

    ![Lista de recomendações do anfitrião do contentor](media/monitor-container-security/container-host-rec.png)

1. Opcionalmente, leia a descrição, informação, ameaças e medidas de reparação. 

1. Selecione **Tomar Ação** na parte inferior da página.

    [![Tome botão de ação](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    O Log Analytics abre com uma operação personalizada pronta a ser executada. A consulta personalizada padrão inclui uma lista de todas as regras falhadas que foram avaliadas, juntamente com diretrizes para ajudá-lo a resolver os problemas.

    [![Ação Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Ajuste os parâmetros de consulta, se necessário.

1. Quando tiver a certeza de que o comando é adequado e pronto para o seu anfitrião, selecione **Run**.



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar as funcionalidades de segurança do Security Center. 

Para outros materiais relacionados, consulte as seguintes páginas: 

- [Recomendações do Centro de Segurança para contentores](recommendations-reference.md#recs-containers)
- [Alertas para o nível de cluster AKS](alerts-reference.md#alerts-akscluster)
- [Alertas para o nível de hospedeiro de contentores](alerts-reference.md#alerts-containerhost)
