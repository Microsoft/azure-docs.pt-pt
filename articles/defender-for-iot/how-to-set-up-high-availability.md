---
title: Configurar a elevada disponibilidade
description: Aumente a resiliência do seu Defender para a implementação de IoT instalando um aparelho de gestão de consolas de alta disponibilidade no local. As implementações de alta disponibilidade garantem que os sensores geridos reportam continuamente a uma consola de gestão ativa no local.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: d0e09cd37fbae91d1903ca8f175c0592b567da6e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781658"
---
# <a name="about-high-availability"></a>Sobre a alta disponibilidade

Aumente a resiliência do seu Defender para a implementação de IoT instalando um aparelho de gestão de consolas de alta disponibilidade no local. As implementações de alta disponibilidade garantem que os sensores geridos reportam continuamente a uma consola de gestão ativa no local.

Esta implementação é implementada com um par de consolas de gestão no local que inclui um aparelho primário e secundário.

## <a name="about-primary-and-secondary-communication"></a>Sobre a comunicação primária e secundária

Quando uma consola de gestão primária e secundária no local é emparelhada:

- É aplicado um certificado SLL de consola de gestão no local para criar uma ligação segura entre os aparelhos primário e secundário. O SLL pode ser o certificado auto-assinado instalado por predefinição ou um certificado instalado pelo cliente.

- Os dados primários da consola de gestão no local são automaticamente apoiados até à consola secundária de gestão no local a cada 10 minutos. As configurações das consolas de gestão no local e os dados do dispositivo são apoiados. Os ficheiros e registos PCAP não estão incluídos na cópia de segurança. Pode fazer o back up e restaurar os PCAPs e os registos manualmente.

- A configuração primária na consola de gestão é duplicada no secundário; por exemplo, configurações do sistema. Se estas definições forem atualizadas nas primárias, também são atualizadas no secundário.

- Antes que a licença do secundário expire, deverá defini-la como a principal para atualizar a licença.

## <a name="about-failover-and-failback"></a>Acerca da ativação pós-falha e da reativação pós-falha

Se um sensor não conseguir ligar-se à consola de gestão principal no local, liga-se automaticamente ao secundário. O seu sistema será suportado tanto pelo primário como pelo secundário simultaneamente, se menos de metade dos sensores estiverem a comunicar com o secundário. O secundário assume quando mais de metade dos sensores estão a comunicar com ele. Falha da primária para a secundária demora aproximadamente três minutos. Quando o failover ocorre, a consola de gestão principal no local congela. Quando isto acontece, pode inscrever-se no secundário usando as mesmas credenciais de inscrição.

Durante o failover, os sensores continuam a tentar comunicar com o aparelho primário. Quando mais de metade dos sensores geridos conseguem comunicar com as primárias, o primário é restaurado. A seguinte mensagem aparece na consola secundária quando a primária é restaurada.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Screenshot de uma mensagem que aparece na consola secundária quando a primária é restaurada.":::

Volte a entrar no aparelho primário após a reorientação.

## <a name="high-availability-setup-overview"></a>Visão geral de configuração de alta disponibilidade

Os procedimentos de instalação e configuração são realizados em quatro fases principais:

1. Instale um aparelho primário de gestão da consola no local. 

2. Configure o aparelho primário de gestão da consola no local. Por exemplo, configurações de backup programadas, definições VLAN. Consulte o guia do utilizador da consola de gestão no local para obter mais detalhes. Todas as definições são aplicadas automaticamente ao aparelho secundário após o emparelhamento.

3. Instale um aparelho secundário de gestão de consolas no local. Para mais informações consulte, [Sobre o Defender para a Instalação IoT](how-to-install-software.md).

4. Emparelhe os aparelhos de consola de gestão primária e secundária no local, conforme [descrito aqui](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console). A consola de gestão principal no local deve gerir pelo menos dois sensores para realizar a configuração.

## <a name="high-availability-requirements"></a>Elevados requisitos de disponibilidade

Verifique se cumpriu os seguintes requisitos de elevada disponibilidade:

- Requisitos de certificados

- Requisitos de software e hardware

- Requisitos de acesso à rede

### <a name="software-and-hardware-requirements"></a>Requisitos de software e hardware

- Tanto os aparelhos de consola de gestão primária como secundário devem estar a executar modelos de hardware idênticos e versões de software.

- O sistema de alta disponibilidade só pode ser criado pelo Defender para utilizadores de IoT, utilizando ferramentas CLI.

### <a name="network-access-requirements"></a>Requisitos de acesso à rede

Tem de verificar se a sua política de segurança organizacional lhe permite aceder aos seguintes serviços na consola de gestão primária e secundária no local. Estes serviços também permitem a ligação entre os sensores e a consola de gestão secundária no local:

|Porta|Serviço|Description|
|----|-------|-----------|
|**443 ou TCP**|HTTPS|Concede acesso à consola web de gestão de consolas no local.|
|**22 ou TCP**|SSH|Sincroniza os dados entre os aparelhos de consola de gestão primária e secundária no local|
|**123 ou UDP**|NTP| A sincronização de tempo ntp da consola de gestão no local. Verifique se os aparelhos ativos e passivos são definidos com o mesmo tempo de tempo.|

## <a name="create-the-primary-and-secondary-pair"></a>Criar o par primário e secundário

Verifique se os aparelhos de consola de gestão primário e secundário no local estão ligados antes de iniciar o procedimento.  

### <a name="on-the-primary"></a>Nas primárias

1. Inscreva-se no CLI como Um Defensor para utilizador IoT.

2. Executar o seguinte comando na primária:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>Neste documento, a principal consola de gestão no local é referida como a principal, e o agente é referido como o secundário.

3. Introduza o endereço IP do aparelho secundário no ```<Secondary ip>``` campo e selecione Enter. O endereço IP é então validado e o certificado SSL é transferido para o principal. A introdução do endereço IP também associa os sensores ao aparelho secundário.

4. Executar o seguinte comando na primária para verificar se o certificado está instalado corretamente:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Executar o seguinte comando na primária. **Não corra com sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Isto permite a ligação entre os aparelhos primário e secundário para fins de backup e restauro entre eles.

6. Introduza o endereço IP do secundário e selecione Enter.

### <a name="on-the-secondary"></a>No secundário

1. Inscreva-se no CLI como Um Defensor para utilizador IoT.

2. Executar o seguinte comando no secundário. **Não corra com sudo:**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Isto permite a ligação entre os aparelhos Primário e Secundário para efeitos de backup e restauro entre eles.

3. Introduza o endereço IP do primário e prima Enter.

### <a name="track-high-availability-activity"></a>Acompanhe a atividade de alta disponibilidade

Os registos de aplicações principais podem ser exportados para a equipa de suporte do Defender para lidar com quaisquer problemas de alta disponibilidade.  

Para aceder aos registos principais:

1. Selecione **Exportar** a partir da janela Definições do **Sistema.**

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Atualize a consola de gestão no local com elevada disponibilidade

Execute a atualização de alta disponibilidade na seguinte encomenda. Certifique-se de que cada passo está completo antes de iniciar um novo passo.

Para atualizar com alta disponibilidade:

1. Atualize a consola de gestão principal no local.

2. Atualize a consola de gestão secundária no local.

3. Atualize os sensores.

## <a name="see-also"></a>Ver também

[Ativar e configurar a sua consola de gestão no local](how-to-activate-and-set-up-your-on-premises-management-console.md)