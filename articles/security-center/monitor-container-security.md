---
title: Monitorização da segurança dos seus contentores no Centro de Segurança Azure
description: Saiba como verificar a postura de segurança dos seus contentores do Centro de Segurança Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 900398a701659bff593df042db16890792e5cffd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744731"
---
# <a name="monitoring-the-security-of-your-containers"></a>Monitorização da segurança dos seus contentores

Esta página explica como utilizar as funcionalidades de segurança do contentor descritas no artigo de Segurança de [Contentores](container-security.md) na nossa secção de conceitos.

O Azure Security Center abrange os seguintes três aspetos da segurança dos contentores:

- **Gestão de vulnerabilidades** - Se estiver no nível de preços padrão do Security Center (ver [preços),](/azure/security-center/security-center-pricing)pode digitalizar o registo de contentores Azure baseado em ARM sempre que uma nova imagem for empurrada. O scanner (alimentado por Qualys) apresenta as conclusões como recomendações do Centro de Segurança.
    Para obter instruções detalhadas, consulte a [verificação dos registos dos seus contentores para obter vulnerabilidades](#scanning-your-arm-based-container-registries-for-vulnerabilities) abaixo.

- **Endurecimento dos anfitriões docker dos seus contentores** - O Centro de Segurança encontra contentores não geridos hospedados em VMs IaaS Linux ou outras máquinas Linux que executam o Docker, e compara continuamente as configurações dos contentores com o Center for Internet Security (CIS) Docker Benchmark. O Centro de Segurança alerta-o se os seus contentores não satisfizerem nenhum dos controlos. A monitorização contínua dos riscos de segurança devido a configurações erradas é um componente crucial de qualquer programa de segurança. 
    Para obter instruções detalhadas, consulte [o Endurecimento dos anfitriões do Docker dos seus contentores](#hardening-your-containers-docker-hosts) abaixo.

- **Endurecendo os seus clusters de serviço Saque Azure Kubernetes** - O Security Center fornece recomendações quando encontra vulnerabilidades na configuração dos seus clusters de Serviço Azure Kubernetes. Para obter detalhes sobre as recomendações específicas que possam aparecer, consulte as recomendações do [Serviço Kubernetes](recommendations-reference.md#recs-containers).

- **Proteção** de tempo de execução - Se estiver no nível padrão de preços do Security Center, terá proteção contra ameaças em tempo real para os seus ambientes contentorizados. O Centro de Segurança gera alertas para atividades suspeitas ao nível do aglomerado de hospedeiros e AKS. Para obter detalhes sobre os alertas de segurança relevantes que possam aparecer, consulte os aglomerados de [serviços Azure Kubernetes](alerts-reference.md#alerts-akscluster) e [alertas para contentores -](alerts-reference.md#alerts-containerhost) secçãos de nível de acolhimento da tabela de referência de alertas.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Digitalizar os registos de contentores baseados em ARM para vulnerabilidades 

1. Para permitir a vulnerabilidade das imagens do registo de contentores do seu Azure:

    1. Certifique-se de que está no nível padrão de preços do Azure Security Center.

    1. A partir da página de **definições** de Preços &, ![ative o pacote opcional de registos de contentores para a sua subscrição: Habilitar o pacote de Registos de Contentores](media/monitor-container-security/enabling-container-registries-bundle.png)

        O Centro de Segurança está agora pronto para digitalizar imagens que são empurradas para o registo. 

        >[!NOTE]
        >Esta funcionalidade é carregada por imagem.


1. Para desencadear a digitalização de uma imagem, empurre-a para o seu registo. 

    Quando a varredura estiver concluída (normalmente após aproximadamente 10 minutos), os resultados estão disponíveis nas recomendações do Centro de Segurança.
    

1. Para ver as descobertas, vá à página de **Recomendações.** Se os problemas forem encontrados, verá a seguinte recomendação:

    ![Recomendação para remediar questões ](media/monitor-container-security/acr-finding.png)


1. Selecione a recomendação. 
    A página de detalhes da recomendação abre com informações adicionais. Esta informação inclui a lista de registos com imagens vulneráveis ("Recursos Afetados") e as etapas de reparação. 

1. Selecione um registo específico para ver os repositórios dentro dele que têm repositórios vulneráveis.

    ![Selecione um registo](media/monitor-container-security/acr-finding-select-registry.png)

    A página de detalhes do registo abre com a lista de repositórios afetados.

1. Selecione um repositório específico para ver os repositórios dentro dele que têm imagens vulneráveis.

    ![Selecione um repositório](media/monitor-container-security/acr-finding-select-repository.png)

    A página de detalhes do repositório abre. Ele enumera as imagens vulneráveis juntamente com uma avaliação da gravidade dos resultados.

1. Selecione uma imagem específica para ver as vulnerabilidades.

    ![Selecione imagens](media/monitor-container-security/acr-finding-select-image.png)

    Abre-se a lista de resultados para a imagem selecionada.

    ![Lista de resultados](media/monitor-container-security/acr-findings.png)

1. Para saber mais sobre uma descoberta, selecione a descoberta. 

    Os detalhes dos resultados abrem-se.

    [![Painel de detalhes de descobertas](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Este painel inclui uma descrição detalhada da questão e ligações a recursos externos para ajudar a mitigar as ameaças.

1. Siga os passos na secção de reparação deste painel.

1. Quando tiver tomado as medidas necessárias para remediar a questão de segurança, substitua a imagem no seu registo:

    1. Empurre a imagem atualizada. Isto vai desencadear uma tomografia. 
    
    1. Consulte a página de recomendações para a recomendação "As vulnerabilidades nas imagens do Registo de Contentores Do Azure devem ser remediadas". 
    
        Se a recomendação ainda aparecer e a imagem que lidou ainda aparecer na lista de imagens vulneráveis, verifique novamente os passos de reparação.

    1. Quando tiver a certeza de que a imagem atualizada foi empurrada, digitalizada e já não aparece na recomendação, apague a imagem vulnerável "antiga" do seu registo.


## <a name="hardening-your-containers-docker-hosts"></a>Endurecendo os anfitriões do Docker dos seus contentores

O Security Center monitoriza constantemente a configuração dos seus anfitriões Docker e gera recomendações de segurança que refletem os padrões da indústria.

Para ver as recomendações de segurança do Azure Security Center para os anfitriões do Docker dos seus contentores:

1. A partir da barra de navegação do Security Center, abra **as aplicações compute &** e selecione o separador **Recipientes.**

1. Opcionalmente, filtre a lista dos seus recursos de contentores para os anfitriões de contentores.

    ![Filtro de recursos de contentores](media/monitor-container-security/container-resources-filter.png)

1. A partir da lista das suas máquinas de acolhimento de contentores, selecione uma para investigar mais.

    ![Recomendações de hospedeiro de contentores](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    A página de informações do **anfitrião** do contentor abre com detalhes do anfitrião e uma lista de recomendações.

1. Na lista de recomendações, selecione uma recomendação para investigar mais.

    ![Lista de recomendação de hospedeiro de contentores](media/monitor-container-security/container-host-rec.png)

1. Opcionalmente, leia a descrição, informações, ameaças e passos de reparação. 

1. Selecione **Tomar medidas** na parte inferior da página.

    [![Tome o botão de ação](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    O Log Analytics abre com uma operação personalizada pronta a ser executada. A consulta personalizada padrão inclui uma lista de todas as regras falhadas que foram avaliadas, juntamente com diretrizes para ajudá-lo a resolver os problemas.

    [![Ação de Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Ajuste os parâmetros de consulta, se necessário.

1. Quando tiver a certeza de que o comando é apropriado e pronto para o seu anfitrião, selecione **Executar**.



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar as funcionalidades de segurança de contentores do Security Center. 

Para outros materiais relacionados, consulte as seguintes páginas: 

- [Recomendações do Centro de Segurança para contentores](recommendations-reference.md#recs-containers)
- [Alertas para o nível de cluster AKS](alerts-reference.md#alerts-akscluster)
- [Alertas para o nível de hospedeiro de contentores](alerts-reference.md#alerts-containerhost)
