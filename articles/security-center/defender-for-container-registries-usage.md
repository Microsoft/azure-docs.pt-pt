---
title: Como utilizar o Azure Defender para registos de contentores
description: Saiba como utilizar o Azure Defender para registos de contentores para digitalizar imagens linux nos seus registos hospedados em Linux
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ee4992e41e792b570d8937edfe31efb4c651d742
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100734"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Utilizar o Azure Defender dos registos de contentores para analisar as imagens quanto a vulnerabilidades

Esta página explica como usar o scanner de vulnerabilidade incorporado para digitalizar as imagens do contentor armazenadas no registo de contentores Azure, baseado em Azure Resource Manager.

Quando o **Azure Defender para registos de contentor** estiver ativado, qualquer imagem que emitir para o registo será imediatamente verificada. Além disso, qualquer imagem puxada nos últimos 30 dias também é digitalizada. 

Quando o scanner reporta vulnerabilidades ao Centro de Segurança, o Centro de Segurança apresenta as conclusões e informações relacionadas como recomendações. Além disso, as conclusões incluem informações relacionadas, tais como medidas de reparação, CVEs relevantes, pontuações de CVSS, e muito mais. Pode ver as vulnerabilidades identificadas para uma ou mais subscrições ou para um registo específico.


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identificar vulnerabilidades em imagens nos registos de contentores do Azure 

Para permitir a verificação de vulnerabilidades de imagens armazenadas no registo do contentor Azure baseado em recursos Azure:

1. Ative **o Azure Defender para registos de contentores** para a sua subscrição. O Centro de Segurança está agora pronto para digitalizar imagens nos seus registos.

    >[!NOTE]
    > Esta funcionalidade é carregada por imagem.

1. As imagens são ativadas em cada impulso ou importação, e se a imagem tiver sido puxada nos últimos 30 dias. 

    Quando a varredura termina (normalmente após aproximadamente 2 minutos, mas pode chegar a 15 minutos), os resultados estão disponíveis como recomendações do Centro de Segurança.

1. [Ver e remediar as conclusões como explicado abaixo](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identificar vulnerabilidades em imagens em outros registos de contentores 

1. Utilize as ferramentas ACR para levar imagens ao seu registo a partir do Docker Hub ou do Microsoft Container Registry.  Quando a importação termina, as imagens importadas são digitalizadas pelo Azure Defender. 

    Saiba mais em [Importar imagens de contentores para um registo de contentores](../container-registry/container-registry-import-images.md)

    Quando a varredura termina (normalmente após aproximadamente 2 minutos, mas pode chegar a 15 minutos), os resultados estão disponíveis como recomendações do Centro de Segurança.

1. [Ver e remediar as conclusões como explicado abaixo](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Ver e remediar as descobertas

1. Para ver os resultados, vá à página **de Recomendações.** Se os problemas forem encontrados, verá a recomendação **Vulnerabilidades nas imagens do Registo de Contentores do Azure devem ser remediadas**

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


## <a name="disable-specific-findings-preview"></a>Desativar as conclusões específicas (pré-visualização)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

Se tiver uma necessidade organizacional de ignorar uma descoberta, em vez de remediar, pode desativá-la opcionalmente. As descobertas desativadas não afetam a sua pontuação segura ou geram ruídos indesejados.

Quando uma descoberta corresponde aos critérios que definiu nas suas regras de desativação, não constará na lista de resultados. Os cenários típicos incluem:

- Desativar as descobertas com severidade abaixo do meio
- Desativar as conclusões que não são remendáveis
- Desativar os resultados com pontuação CVSS abaixo de 6.5
- Desativar as conclusões com texto específico na verificação ou categoria de segurança (por exemplo, "RedHat", "CentOS Security Update for sudo")

> [!IMPORTANT]
> Para criar uma regra, precisa de permissões para editar uma política na Política Azure.
>
> Saiba mais nas [permissões Azure RBAC na Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

Pode utilizar qualquer um dos seguintes critérios: 

- Encontrar ID 
- Categoria
- Verificação de segurança 
- Pontuações cvss v3
- Gravidade 
- Estado remendado 

Para criar uma regra:

1. A partir da página de pormenor de recomendações para **vulnerabilidades em imagens do registo de contentores Azure deve ser remediada**, selecione **Disable rule**.
1. Selecione o âmbito relevante.
1. Defina os seus critérios.
1. **Selecione Aplicar a regra**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Criar uma regra de desativação para as conclusões do VA no registo":::

1. Para ver, anular ou apagar uma regra: 
    1. **Selecione Regra de desativação**.
    1. Da lista de âmbito, as subscrições com regras ativas mostram **como regra aplicada**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Modificar ou eliminar uma regra existente":::
    1. Para visualizar ou eliminar a regra, selecione o menu de elipses ("...").


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure Defender](azure-defender.md)