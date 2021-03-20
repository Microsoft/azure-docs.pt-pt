---
title: Implementar Qualidade de Serviço (QoS) para Windows Virtual Desktop (pré-visualização)
titleSuffix: Azure
description: Como configurar o QoS (pré-visualização) para o Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94639375"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Implementar Qualidade de Serviço (QoS) para Windows Virtual Desktop (pré-visualização)

> [!IMPORTANT]
> O suporte de política de qualidade de serviço (QoS) para o Windows Virtual Desktop está atualmente em pré-visualização pública.
> Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-lo para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[O RDP Shortpath](./shortpath.md) fornece um transporte direto baseado na UDP entre o cliente de desktop remoto e o anfitrião da sessão. O Via-Curto RDP permite a configuração das políticas de Qualidade de Serviço (QoS) para os dados rdp.
O QoS no Windows Virtual Desktop permite que o tráfego RDP em tempo real sensível aos atrasos da rede "corte na linha" em frente ao tráfego menos sensível. Exemplo de tráfego tão menos sensível seria o descarregamento de uma nova app, onde um segundo extra para descarregar não é um grande negócio. O QoS utiliza o Windows Group Policy Objects para identificar e marcar todos os pacotes em streams em tempo real e ajudar a sua rede a dar ao tráfego RDP uma porção dedicada de largura de banda.

Se você apoiar um grande grupo de utilizadores que experimentam qualquer um dos problemas descritos neste artigo, você provavelmente precisa implementar QoS. Uma pequena empresa com poucos utilizadores pode não precisar de QoS, mas deve ser útil mesmo lá.

Sem alguma forma de QoS, pode ver as seguintes questões:

* Jitter – Pacotes RDP que chegam a taxas diferentes, o que pode resultar em falhas visuais e áudio
* Perda de pacotes – pacotes caídos, o que resulta em retransmissão que requer tempo adicional
* Atraso no tempo de ida e volta (RTT) – Pacotes RDP demoram muito tempo a chegar aos seus destinos, o que resulta em atrasos visíveis entre a entrada e a reação da aplicação remota.

A forma menos complicada de abordar estas questões é aumentar o tamanho das ligações de dados, tanto internamente como para a internet. Uma vez que isso é muitas vezes proibitivo em termos de custos, o QoS fornece uma forma de gerir os recursos que tem em vez de adicionar largura de banda de forma mais eficaz. Para resolver problemas de qualidade, recomendamos que use primeiro o QoS e, em seguida, adicione largura de banda apenas quando necessário.

Para que o QoS seja eficaz, deve aplicar configurações QoS consistentes em toda a sua organização. Qualquer parte do caminho que não suporte as suas prioridades QoS pode degradar a sessão DE PDR de qualidade.

## <a name="introduction-to-qos-queues"></a>Introdução às filas QoS

Para fornecer QoS, os dispositivos de rede devem ter uma forma de classificar o tráfego e devem ser capazes de distinguir RDP de outro tráfego de rede.

Quando o tráfego de rede entra num router, o tráfego é colocado numa fila. Se uma política QoS não estiver configurada, há apenas uma fila, e todos os dados são tratados como primeiro-a-entrar, primeiro a sair com a mesma prioridade. Isso significa que o tráfego rdp pode ficar preso atrás do trânsito onde alguns milissegundos extra atrasos não seriam um problema.

Ao implementar o QoS, define várias filas utilizando uma das várias funcionalidades de gestão de congestionamento, tais como a fila prioritária da Cisco e as funcionalidades [de fila ponderada ponderada (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) e prevenção do congestionamento, tais como a [deteção precoce aleatória ponderada (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Uma simples analogia é que o QoS cria "carpool lanes" virtuais na sua rede de dados. Assim, alguns tipos de dados nunca ou raramente encontram um atraso. Uma vez que você cria essas faixas, você pode ajustar o seu tamanho relativo e muito mais eficazmente gerir a largura de banda de conexão que você tem enquanto ainda oferece experiências de nível de negócio para os utilizadores da sua organização.

## <a name="qos-implementation-checklist"></a>Lista de verificação de implementação do QoS

A um nível elevado, faça o seguinte para implementar o QoS:

1. [Certifique-se de que a sua rede está pronta](#make-sure-your-network-is-ready)
2. [Certifique-se de que o Via 3P Shortpath está ativado](./shortpath.md) - as políticas QoS não são suportadas para o transporte de ligação inversa
3. [Implementar inserção de marcadores DSCP](#insert-dscp-markers) nos anfitriões da sessão

Enquanto se prepara para implementar o QoS, tenha em mente as seguintes orientações:

* O caminho mais curto para o anfitrião da sessão é o melhor
* Quaisquer obstáculos entre eles, tais como proxies ou dispositivos de inspeção de pacotes, não são recomendados

## <a name="make-sure-your-network-is-ready"></a>Certifique-se de que a sua rede está pronta

Se está a considerar uma implementação QoS, já deveria ter determinado os seus requisitos de largura de banda e [outros requisitos de rede](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context).
  
O congestionamento do tráfego através de uma rede terá um impacto significativo na qualidade dos meios de comunicação social. A falta de largura de banda leva à degradação do desempenho e a uma má experiência do utilizador. À medida que a adoção e utilização do Windows Virtual Desktop cresce, use [o Log Analytics](./diagnostics-log-analytics.md) para identificar problemas e, em seguida, faça ajustes usando QoS e adições seletivas de largura de banda.

### <a name="vpn-considerations"></a>Considerações VPN

O QoS só funciona como esperado quando implementado em todas as ligações entre clientes e anfitriões de sessões. Se utilizar o QoS numa rede interna e um utilizador entrar numa localização remota, só pode priorizar dentro da sua rede interna e gerida. Embora as localizações remotas possam receber uma ligação gerida implementando uma rede privada virtual (VPN), uma VPN adiciona inerentemente a sobrecarga do pacote e cria atrasos no tráfego em tempo real.

Numa organização global com ligações geridas que abrangem continentes, recomendamos vivamente o QoS porque a largura de banda para essas ligações é limitada em comparação com a LAN.

## <a name="insert-dscp-markers"></a>Inserir marcadores DSCP

Pode implementar o QoS utilizando um Objeto de Política de Grupo (GPO) para dirigir anfitriões de sessão para inserir um marcador DSCP em cabeçalhos de pacote IP identificando-o como um tipo particular de tráfego. Os routers e outros dispositivos de rede podem ser configurados para reconhecer estas marcas e colocar o tráfego numa fila separada e de maior prioridade.

Pode comparar as marcações DSCP com os selos postais que indicam aos trabalhadores dos correios a urgência da entrega e a melhor forma de a classificar para entrega rápida. Uma vez configurado a sua rede para dar prioridade aos fluxos RDP, os pacotes perdidos e os pacotes tardios devem diminuir significativamente.

Uma vez que todos os dispositivos de rede estão usando as mesmas classificações, marcações e prioridades, é possível reduzir ou eliminar atrasos, pacotes abandonados e nervosismo. Do ponto de vista do PDR, o passo essencial de configuração é a classificação e marcação de pacotes. No entanto, para que o QoS de ponta a ponta seja bem sucedido, também é necessário alinhar cuidadosamente a configuração RDP com a configuração de rede subjacente.
O valor DSCP diz a uma rede correspondentemente configurada qual a prioridade de dar um pacote ou fluxo.

Recomendamos a utilização do valor DSCP 46 que mapeia para a classe DSCP **de Reencaminhamento Expedito (EF).**

### <a name="implement-qos-on-session-host-using-group-policy"></a>Implementar QoS no anfitrião da sessão usando a Política do Grupo

Pode utilizar a Qualidade de Serviço (QoS) baseada em políticas no âmbito da Política de Grupo para definir o valor DSCP predefinido.

Para criar uma política QoS para anfitriões de sessão ligados ao domínio, em primeiro lugar, inscreva-se num computador no qual a Gestão de Políticas de Grupo foi instalada. Abrir a gestão da política do grupo (selecione Start, apontar para Ferramentas Administrativas e, em seguida, selecionar Gestão de Políticas de Grupo) e, em seguida, completar os seguintes passos:

1. Na Gestão da Política de Grupo, localize o contentor onde deve ser criada a nova política. Por exemplo, se todos os computadores anfitriões de sessão estiverem localizados num OU chamado **"hosts de sessão",** a nova política deve ser criada no Session Hosts OU.

2. Clique com o botão direito no recipiente apropriado e, em seguida, **selecione Criar um GPO neste domínio e ligá-lo aqui**.

3. Na nova caixa de diálogo **GPO,** digite um nome para o novo objeto de Política de Grupo na caixa **Nome** e, em seguida, selecione **OK**.

4. Clique com o direito na política recém-criada e, em seguida, **selecione Editar**.

5. No Editor de Gestão de Políticas de Grupo, expanda **a configuração do computador,** expanda **as definições do Windows,** clique com o botão direito **QoS** e, em seguida, selecione **Criar nova política**.

6. Na caixa de diálogo **QoS baseada em políticas,** na página de abertura, escreva um nome para a nova política na caixa **Nome.** **Selecione Especificar O Valor DSCP** e definir o valor para **46**. Deixe **especificar a taxa de aceleração de saída** não selecionada e, em seguida, selecione **Seguinte**.

7. Na página seguinte, selecione **Apenas aplicações com este nome executável** e introduza o nome **svchost.exe**, e, em seguida, selecione **Next**. Esta definição instrui a política para apenas priorizar o tráfego correspondente do Serviço de Ambiente de Trabalho Remoto.

8. Na terceira página, certifique-se de que tanto qualquer **endereço IP de origem** **como qualquer endereço IP de destino** estão selecionados e, em seguida, selecione **Seguinte**. Estas duas definições garantem que os pacotes serão geridos independentemente do computador (endereço IP) que enviou os pacotes e que computador (endereço IP) receberá os pacotes.

9. Na página quatro, selecione **UDP** do **Select the protocol this QoS policy applie to** drop-down list.

10. Sob a posição **Especifique o número da porta de origem**, selecione **A partir desta porta de origem ou alcance**. Na caixa de texto que o acompanha, tipo **3390**. Selecione **Concluir**.

As novas políticas que criou não entrarão em vigor até que a Política de Grupo seja atualizada nos computadores anfitriões da sessão. Embora a Política de Grupo refresque periodicamente por si só, pode forçar uma atualização imediata seguindo estes passos:

1. Em cada anfitrião de sessão para o qual pretende atualizar a Política de Grupo, abra um Aviso de Comando como administrador *(Executar como administrador*).

1. Na pronta do comando, insira

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>Implementar QoS no anfitrião da sessão usando PowerShell

Pode definir QoS para RdP Shortpath utilizando o cmdlet PowerShell abaixo:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Artigos relacionados

* [Política de Qualidade de Serviço (QoS)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os requisitos de largura de banda para o Windows Virtual Desktop, consulte [os requisitos de largura de banda do Protocolo de Ambiente de Trabalho Remoto (RDP) para o Windows Virtual Desktop](rdp-bandwidth.md).
* Para saber mais sobre a conectividade da rede virtual do Windows Desktop, consulte [a conectividade da rede virtual de desktop do Windows.](network-connectivity.md)
