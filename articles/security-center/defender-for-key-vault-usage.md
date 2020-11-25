---
title: Como responder aos alertas do Azure Defender para o Cofre de Chaves
description: Conheça os passos necessários para responder aos alertas do Azure Defender para o Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122208"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Responder a alertas do Azure Defender para o Key Vault
Quando receber um alerta do Azure Defender para o Key Vault, recomendamos que investigue e responda ao alerta conforme descrito abaixo. O Azure Defender for Key Vault protege aplicações e credenciais, por isso mesmo que esteja familiarizado com a aplicação ou utilizador que desencadeou o alerta, é importante verificar a situação em torno de cada alerta.  

Cada alerta do Azure Defender para o Key Vault inclui os seguintes elementos:

- ID de objeto
- Nome principal do utilizador ou endereço IP do recurso suspeito

> [!TIP]
> Com base no *tipo* de acesso que ocorreu, alguns campos podem não estar disponíveis. Por exemplo, se o seu cofre chave foi acedido por uma aplicação, não verá um nome principal do utilizador associado. Se o tráfego tiver origem no exterior de Azure, não verá um ID de objeto.

## <a name="step-1-contact"></a>Passo 1. Contacto

1. Verifique se o tráfego teve origem no seu inquilino Azure. Se a firewall do cofre está ativada, é provável que tenha dado acesso ao utilizador ou aplicação que desencadeou este alerta.
1. Se não puder verificar a origem do tráfego, continue até ao [passo 2. Atenuação imediata](#step-2-immediate-mitigation).
1. Se conseguir identificar a origem do tráfego no seu inquilino, contacte o utilizador ou proprietário da aplicação. 

> [!CAUTION]
> O Azure Defender for Key Vault foi concebido para ajudar a identificar atividades suspeitas causadas por credenciais roubadas. **Não** desprezem o alerta simplesmente porque reconhecem o utilizador ou a aplicação. Contacte o proprietário da aplicação ou o utilizador e verifique se a atividade era legítima. Pode criar uma regra de supressão para eliminar o ruído, se necessário. Saiba mais em [alertas da Azure Defender](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>Passo 2. Atenuação imediata 
Se não reconhecer o utilizador ou aplicação, ou se acha que o acesso não deveria ter sido autorizado:

- Se o tráfego veio de um endereço IP não reconhecido:
    1. Ativar a firewall Azure Key Vault, tal como descrito nas [firewalls Configure Key Vault e redes virtuais](../key-vault/general/network-security.md).
    1. Configure a firewall com recursos fidedignos e redes virtuais.

- Se a origem do alerta fosse uma aplicação não autorizada ou um utilizador suspeito:
    1. Abra as definições de política de acesso do cofre da chave.
    1. Remova o correspondente principal de segurança ou restringa as operações que o principal de segurança pode executar.  

- Se a fonte do alerta tiver um papel de Diretório Ativo Azure no seu inquilino:
    1. Contacte o seu administrador.
    1. Determine se há necessidade de reduzir ou revogar permissões do Azure Ative Directory.

## <a name="step-3-identify-impact"></a>Passo 3. Identificar impacto 
Quando o impacto tiver sido atenuado, investigue os segredos do seu cofre chave que foram afetados:
1. Abra a página "Segurança" no cofre da chave Azure e veja o alerta desencadeado.
1. Selecione o alerta específico que foi desencadeado.
    Reveja a lista dos segredos que foram acedidos e a hora da hora.
1. Opcionalmente, se tiver registos de diagnóstico de cofre de chaves ativados, reveja as operações anteriores para o IP, o principal do utilizador ou o ID do objeto correspondente.  

## <a name="step-4-take-action"></a>Passo 4: Tomar medidas 
Quando tiver compilado a sua lista de segredos, chaves e certificados que foram acedidos pelo utilizador ou aplicação suspeito, deverá rodar esses objetos imediatamente.

1. Os segredos afetados devem ser desativados ou eliminados do cofre da chave.
1. Se as credenciais forem utilizadas para uma aplicação específica:
    1. Contacte o administrador do pedido e peça-lhes que auditem o seu ambiente para qualquer utilização das credenciais comprometidas, uma vez que foram comprometidas.
    1. Se as credenciais comprometidas forem utilizadas, o titular da aplicação deve identificar as informações que foram acedidas e mitigar o impacto.


## <a name="next-steps"></a>Passos seguintes

Esta página explicou o processo de resposta a um alerta do Azure Defender para o Key Vault. Para obter informações relacionadas consulte as seguintes páginas:

- [Introdução ao Azure Defender para o Cofre de Chaves](defender-for-key-vault-introduction.md)
- [Alertas de Supressão do Azure Defender](alerts-suppression-rules.md)
- [Exportar continuamente dados do Centro de Segurança](continuous-export.md)