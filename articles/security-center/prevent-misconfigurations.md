---
title: Como prevenir erros de configuração com o Centro de Segurança Azure
description: Saiba como utilizar as opções de "Enforce" e 'Deny' do Security Center nas páginas de detalhes das recomendações
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906398"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Prevenir configurações erradas com recomendações de Aplicação/Negação

As configurações erradas de segurança são a principal causa de incidentes de segurança. O Centro de Segurança tem agora a capacidade de ajudar a *prevenir* a má configuração de novos recursos no que diz respeito a recomendações específicas. 

Esta função pode ajudar a manter as suas cargas de trabalho seguras e estabilizar a sua pontuação segura.

A aplicação de uma configuração segura, baseada numa recomendação específica, é oferecida em dois modos:

- Usando o efeito **Deny** da Política Azure, você pode impedir que recursos insalubres sejam criados
- Utilizando a opção **Enforce,** pode tirar partido do efeito **DeployIfNotExist** da política da Azure e remediar automaticamente recursos não conformes após a criação

Isto pode ser encontrado no topo da página de detalhes de recursos para recomendações de segurança selecionadas (ver [Recomendações com opções de negação/execução).](#recommendations-with-denyenforce-options)

## <a name="prevent-resource-creation"></a>Prevenir a criação de recursos

1. Abra a recomendação de que os seus novos recursos devem satisfazer e selecione o botão **Deny** no topo da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Página de recomendação com botão Deny realçado":::

    O painel de configuração abre a listagem das opções de âmbito. 

1. Desacione o âmbito selecionando o grupo de subscrição ou gestão relevante.

    > [!TIP]
    > Pode utilizar os três pontos no final da linha para alterar uma única subscrição, ou utilizar as caixas de verificação para selecionar várias subscrições ou grupos e, em seguida, selecionar **Alterar para Negar**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Página de recomendação com botão Deny realçado":::


## <a name="enforce-a-secure-configuration"></a>Impor uma configuração segura

1. Abra a recomendação de que irá implementar uma implementação de modelo para se novos recursos não satisfazê-lo, e selecione o botão **Enforce** no topo da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Página de recomendação com botão Deny realçado":::

    O painel de configuração abre-se com todas as opções de configuração de política. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Página de recomendação com botão Deny realçado":::

1. Desa esta vista, nome de atribuição e outras opções relevantes.

1. Selecione **Rever + criar**.

## <a name="recommendations-with-denyenforce-options"></a>Recomendações com opções de negação/aplicação

Estas recomendações podem ser utilizadas com a opção **de negação:**

- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure
- Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nomes do Event Hub
- Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nomes do Service Bus
- A transferência segura para contas de armazenamento deve ser ativada
- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- As variáveis de conta de automação devem ser encriptadas
- Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign
- Auditoria acesso ilimitado à rede a contas de armazenamento


Estas recomendações podem ser utilizadas com a opção **de aplicação:**

- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados
- Os registos de diagnóstico em Data Lake Analytics devem ser ativados
- Os registos de diagnóstico no IoT Hub devem ser ativados
- Os registos de diagnóstico nas contas do Lote devem ser ativados
- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados
- Os registos de diagnóstico no Service Bus devem ser ativados
- Os registos de diagnóstico nos serviços de pesquisa devem ser ativados
- Os registos de diagnóstico no Centro de Eventos devem ser ativados
- Os registos de diagnóstico em conjuntos de escala de máquina virtual devem ser ativados
- Os registos de diagnóstico no Cofre de Chaves devem ser ativados
- A auditoria no servidor SQL deve ser ativada
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL



