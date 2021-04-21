---
title: Atualizar dados de inteligência de ameaças
description: O pacote de dados de inteligência de ameaça é fornecido com cada novo Defender para a versão IoT, ou se necessário entre lançamentos.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750469"
---
# <a name="threat-intelligence-research-and-packages"></a>Pesquisa e pacotes de inteligência de ameaça #
## <a name="overview"></a>Descrição Geral ##

As equipas de segurança da Microsoft realizam pesquisas de inteligência e vulnerabilidade de ameaças do ICS. Estas equipas incluem MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), DCU (Digital Crimes Unit) e Section 52 (especialistas em domínio IoT/OT/ICS que rastreiam zero dias específicos do ICS, malware de engenharia inversa, campanhas e adversários)

As equipas fornecem deteção de segurança, análise e resposta à Microsoft:

- Infraestruturas e serviços em nuvem.
- Produtos e dispositivos tradicionais.
- Recursos corporativos internos.

As equipas de segurança beneficiam de:

- Proteção contra ameaças conhecidas e relevantes.
- Insights que ajudam a triagem e priorizam.
- Uma compreensão do contexto completo das ameaças antes de serem afetadas.
- Dados mais relevantes, precisos e accuíveis.

Esta inteligência fornece informações contextuais para enriquecer a análise da plataforma da Microsoft e suporta os serviços geridos da empresa para a resposta a incidentes e investigação de violação. Os pacotes de inteligência de ameaça contêm assinaturas (incluindo assinaturas de malware), CVEs e outros conteúdos de segurança.

## <a name="when-are-packages-delivered"></a>Quando os pacotes são entregues ##

Os pacotes de inteligência de ameaça são fornecidos aproximadamente uma vez por mês, ou se necessário com mais frequência. Anúncios sobre novos pacotes estão disponíveis a partir de: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Atualizar pacotes de inteligência de ameaças para os seus sensores ##

Estão disponíveis três opções para atualizar pacotes de inteligência de ameaças para os seus sensores:

- Empurre automaticamente as encomendas para os sensores à medida que são entregues pelo Defender para IoT.
- Empurre manualmente o pacote de inteligência de ameaça para os sensores, conforme necessário.
- Faça o download de um pacote e, em seguida, faça o upload para um sensor ou vários sensores.

Os utilizadores com permissões defender para o Leitor de Segurança IoT podem empurrar automaticamente e manualmente os pacotes para os sensores.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Empurre automaticamente as atualizações de inteligência de ameaças para os sensores ###

Os pacotes de inteligência de ameaça podem ser automaticamente atualizados para sensores ligados à *nuvem,* uma vez que são lançados pelo Defender para IoT. Certifique-se de uma atualização automática do pacote, a bordo do sensor ligado à nuvem com a opção **De Atualizações de Inteligência de Ameaça Automática** ativada. Para obter mais informações, consulte [a bordo de um sensor.](getting-started.md#onboard-a-sensor)

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Empurre manualmente atualizações de inteligência de ameaça para sensores ###

Os seus sensores *ligados à nuvem* podem ser automaticamente atualizados com pacotes de inteligência de ameaça. No entanto, se quiser ter uma abordagem mais conservadora, pode empurrar pacotes do portal Azure Defender para ioT para sensores apenas quando sentir que é necessário.
Isto dá-lhe a capacidade de controlar quando um pacote é instalado, sem a necessidade de descarregar e depois carregá-lo para os seus sensores.

**Para empurrar manualmente os pacotes:**

1. Aceda à página Azure Defender para **sites e sensores** IoT.
1. Selecione a elipse (...) para um sensor e, em seguida, selecione **a atualização push threat intelligence**. O campo **de atualização da atualização da Inteligência ameaça** mostra o progresso da atualização.

#### <a name="change-the-threat-intelligence-update-mode"></a>Alterar o modo de atualização de inteligência de ameaça ####

Pode alterar o modo de atualização da inteligência da ameaça do sensor após a primeira entrada no embarque.

**Para alterar o modo de atualização:**

1. Selecione a elipse (...) para um sensor e, em seguida, **selecione Editar**.
1. Ative ou desative as **atualizações automáticas de inteligência de ameaças.**

### <a name="download-packages-and-upload-to-sensors"></a>Descarregue pacotes e faça upload para sensores ###

Os pacotes podem ser descarregados a partir do portal Defender para IoT e enviados manualmente para sensores individuais. Se a consola de gestão no local gerir os seus sensores, pode transferir pacotes de inteligência de ameaças para a consola de gestão e empurrá-los para vários sensores simultaneamente.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Faça o download de atualizações através do portal Azure Defender para IoT.":::

Esta opção está disponível tanto para sensores *ligados à nuvem* como para sensores *geridos localmente.*

**Para fazer o upload para um único sensor:**

1. Aceda à página Azure Defender para **atualizações** IoT.

2. Descarregue e guarde o pacote **de Inteligência ameaça.**

3. Inscreva-se na consola do sensor.

4. No menu lateral, selecione **Definições do Sistema**.

5. Selecione **dados de inteligência de ameaça** e, em seguida, selecione **Update**.

6. Faça o upload do novo pacote.

**Para carregar em simultâneo para vários sensores:**

1. Aceda à página Azure Defender para **atualizações** IoT.

2. Descarregue e guarde o pacote **de Inteligência ameaça.**

3. Inscreva-se na consola de gestão.

4. No menu lateral, selecione **Definições do Sistema**.

5. Na secção **de Configuração do Motor do Sensor,** selecione os sensores que devem receber os pacotes atualizados.  

6. Na secção **Select Threat Intelligence Data,** selecione o sinal mais **+** ().

7. Faça o upload do pacote.

## <a name="review-package-update-status-on-the-sensor"></a>Rever o estado da atualização do pacote no sensor ##

As informações sobre o estado e a versão da atualização do pacote são apresentadas nas **Definições do Sistema** do sensor , Secção de Inteligência de **Ameaças.**  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Reveja as informações do pacote para sensores ligados à nuvem ##

Reveja as seguintes informações sobre pacotes de inteligência de ameaças para os seus sensores ligados à nuvem:

- Versão do pacote instalada
- Modo de atualização de inteligência de ameaça
- Estado de atualização de inteligência de ameaça

Para rever informações de inteligência de ameaças:

1. Aceda à página Azure Defender para **sites e sensores** IoT.
1. Reveja a **versão "Threat Intelligence"** instalada em cada sensor. O nome da versão baseia-se no dia em que o pacote foi construído pelo Defender para IoT.
1. Reveja o **modo de Inteligência de Ameaça.** *O automático* indica que os pacotes recém-disponibilizados serão automaticamente instalados nos sensores, uma vez que são lançados pelo Defender para IoT. *O manual* indica que pode empurrar os pacotes recém-disponíveis diretamente para os sensores, se necessário.
1. Reveja o estado de **atualização da Inteligência de Ameaça.** Podem ser apresentados os seguintes estados:

- Com falhas
- Em Curso
- Atualização Disponível
- Ok

Se as atualizações de inteligência de ameaça ligadas à nuvem falharem, reveja as informações de ligação no estado do **sensor** e nas colunas **UTC conectadas pela última vez** na página De Sites e **Sensores.** 

## <a name="see-also"></a>Ver também

[A bordo de um sensor](getting-started.md#onboard-a-sensor)

[Gerir sensores a partir da consola de gestão](how-to-manage-sensors-from-the-on-premises-management-console.md)