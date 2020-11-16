---
title: Requisitos de largura de banda do Windows Virtual Desktop - Azure
titleSuffix: Azure
description: Compreender os requisitos de largura de banda RDP para o Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 708ffce014f417f3794e59e1f79a3fcf9cba3f23
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639271"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Requisitos de largura de banda do Protocolo remoto de Ambiente de Trabalho (RDP)

Remote Desktop Protocol (RDP) é uma tecnologia sofisticada que utiliza várias técnicas para aperfeiçoar a entrega remota de gráficos do servidor ao dispositivo cliente. Dependendo do caso de utilização, disponibilidade de recursos informáticos e largura de banda de rede, o RDP ajusta dinamicamente vários parâmetros para proporcionar a melhor experiência do utilizador.

O Protocolo remoto de Ambiente de Trabalho multiplexes múltiplos Canais Virtuais Dinâmicos (DVCs) num único canal de dados enviado por diferentes transportes de rede. Existem DVCs separados para gráficos remotos, entrada, reorientação do dispositivo, impressão, entre outros. A WVD Partners também pode implementar as suas extensões que utilizam interfaces DVC.
A quantidade dos dados enviados através do PDR depende da atividade do utilizador. Por exemplo, um utilizador pode trabalhar com conteúdo textual básico durante a maior parte da sessão e consumir a largura de banda mínima, mas depois gerar uma impressão de um documento de 200 páginas para a impressora local. Esta impressão utilizará uma quantidade significativa de largura de banda de rede.

Ao utilizar uma sessão remota, a largura de banda disponível da sua rede impacta drasticamente a qualidade da sua experiência. Diferentes aplicações e resoluções de exibição requerem configurações de rede diferentes, por isso é essencial garantir que a configuração da sua rede satisfaz as suas necessidades.

## <a name="estimating-bandwidth-utilization"></a>Estimativa da utilização da largura de banda

O RDP utiliza vários algoritmos de compressão para diferentes tipos de dados. A tabela abaixo guia a estimativa das transferências de dados:

| Tipo de dados | Direção | Como estimar |
|---|---|---|
| Gráficos remotos | Anfitrião de sessão para cliente | [Consulte as diretrizes detalhadas](#estimating-bandwidth-used-by-remote-graphics) |
| Batimentos cardíacos | Ambas as direções | ~ 20 bytes a cada 5 segundos  |
| Entrada | Cliente para sessão Anfitrião | A quantidade de dados baseia-se na atividade do utilizador, menos de 100 bytes para a maioria das operações  |
| Transferências de ficheiros | Ambas as direções | As transferências de ficheiros estão a usar compressão a granel. Utilize .zip compressão para aproximação |
| Impressão | Anfitrião de sessão para cliente | A transferência de trabalho de impressão depende do condutor e da compressão a granel, utilize .zip compressão para aproximação |

Outros cenários podem ter os seus requisitos de largura de banda alterados dependendo da forma como os utiliza, tais como:

* Conferência de voz ou vídeo
* Comunicação em tempo real
* Streaming vídeo 4K

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Estimativa da largura de banda usada por gráficos remotos

É difícil prever o uso de largura de banda pelo ambiente de trabalho remoto. As atividades do utilizador geram a maior parte do tráfego remoto de desktop. Cada utilizador é único e as diferenças nos seus padrões de trabalho podem alterar significativamente o uso da rede.

A melhor maneira de compreender os requisitos de largura de banda é monitorizar as ligações reais do utilizador. A monitorização pode ser efetuada pelos balcões de desempenho incorporados ou pelo equipamento de rede.

No entanto, em muitos casos, pode estimar a utilização da rede, compreendendo como funciona o Protocolo remoto de Ambiente de Trabalho e analisando os padrões de trabalho dos seus utilizadores.

O protocolo remoto fornece os gráficos gerados pelo servidor remoto para exibi-lo num monitor local. Mais especificamente, fornece o bitmap de ambiente de trabalho inteiramente composto no servidor.
Embora o envio de um mapa de bits de ambiente de trabalho pareça uma tarefa simples à primeira abordagem, requer uma quantidade significativa de recursos. Por exemplo, uma imagem de ambiente de trabalho de 1080p na sua forma não comprimida tem cerca de 8Mb de tamanho. Exibir esta imagem no monitor ligado localmente com uma taxa modesta de atualização de ecrã de 30 Hz requer largura de banda de cerca de 237 MB/s.

Para reduzir a quantidade de dados transferidos sobre a rede, o RDP utiliza a combinação de múltiplas técnicas, incluindo, mas não se limitando a

* Otimizações da taxa de fotogramas
* Classificação de conteúdo de tela
* Codecs específicos de conteúdo
* Codificação progressiva da imagem
* Caching do lado do cliente

Para melhor compreender os gráficos remotos, considere o seguinte:

* Quanto mais ricos os gráficos, mais largura de banda será necessário
  * Texto, elementos de UI de janela e áreas de cores sólidas estão a consumir menos largura de banda do que qualquer outra coisa.
  * As imagens naturais são os contribuintes mais significativos para o uso da largura de banda. Mas o caching do lado do cliente ajuda com a sua redução.
* Apenas são transmitidas partes alteradas do ecrã. Se não houver atualizações visíveis no ecrã, não são enviadas atualizações.
* A reprodução de vídeo e outros conteúdos de alta taxa de fotogramas são essencialmente uma apresentação de diapositivos de imagem. RdP utiliza dinamicamente códigos de vídeo apropriados para entregá-los com a taxa de fotogramas próxima ao original. No entanto, ainda é gráfico, e ainda é o contribuinte mais significativo para a utilização da largura de banda.
* Tempo de marcha lenta em ambiente de trabalho remoto significa não atualizações de ecrã mínimas ou mínimas; assim, a utilização da rede é mínima durante os tempos de marcha lenta.
* Quando a janela do cliente do ambiente de trabalho remoto é minimizada, não são enviadas atualizações gráficas do anfitrião da sessão.

Tenha em mente que o stress colocado na sua rede depende tanto da taxa de produção da sua aplicação como da resolução do seu ecrã. Se a taxa de fotogramas ou a resolução do ecrã aumentarem, o requisito de largura de banda também aumentará. Por exemplo, uma carga de trabalho leve com um ecrã de alta resolução requer mais largura de banda disponível do que uma carga de trabalho leve com resolução regular ou baixa. Diferentes resoluções de exibição requerem diferentes larguras de banda disponíveis.

A tabela abaixo guia a estimativa dos dados utilizados pelos diferentes cenários gráficos. Estes números aplicam-se a uma única configuração de monitor com resolução 1920x1080 e com o modo gráfico predefinido e o modo gráfico H.264/AVC 444.

| Cenário | Modo predefinido | Modo H.264/AVC 444 | Miniatura | Descrição do cenário |
|:---|---:|---:|---|---|
| Períodos | 0,3 Kbps | 0,3 Kbps |:::image type="content" source="media/idle.png" alt-text="Screenshot da ligação ociosa":::| O utilizador está parado no seu trabalho e não há atualizações de ecrã ativa |
| Microsoft Word | 100-150 Kbps | 200-300 Kbps |:::image type="content" source="media/word.gif" alt-text="Animação do Microsoft Word":::| O utilizador está a trabalhar ativamente com o Microsoft Word, digitando, colando gráficos e alternando entre documentos |
| Microsoft Excel | 150-200 Kbps | 400-500 Kbps |:::image type="content" source="media/excel.gif" alt-text="Animação do Microsoft Excel":::| O utilizador está a trabalhar ativamente com o Microsoft Excel, várias células com fórmulas e gráficos são atualizados simultaneamente |
| Microsoft PowerPoint | 4-4.5 Mbps | 1.6-1.8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="Animação do Microsoft PowerPoint":::| O utilizador está a trabalhar ativamente com o Microsoft PowerPoint, digitando, colagem. USer também modifica gráficos ricos, e usando efeitos de transição de slide |
| Navegação na Web | 6-6.5 Mbps | 0,9-1 Mbps |:::image type="content" source="media/web.gif" alt-text="Animação da navegação na Web":::| O utilizador está a trabalhar ativamente com um site graficamente rico que contém múltiplas imagens estáticas e animadas. O utilizador percorre as páginas tanto horizontal como verticalmente |
| Galeria de Imagens | 3.3-3.6 Mbps | 0.7-0.8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="Animação da galeria de imagens":::| O utilizador está a trabalhar ativamente com a aplicação da galeria de imagens. navegação, zoom, redimensionamento e rotação de imagens |
| Reproduzir vídeo | 8.5-9.5 Mbps | 2.5-2.8 Mbps |:::image type="content" source="media/video.gif" alt-text="Animação da reprodução de vídeo":::| O utilizador está a ver um vídeo de 30 FPS que consome 1/2 do ecrã |
| Reprodução de vídeo de ecrã completo | 7.5-8.5 Mbps | 2.5-3.1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animação da reprodução de vídeo fullscreen":::| O utilizador está a ver um vídeo de 30 FPS que maximizou para um ecrã completo |

## <a name="dynamic-bandwidth-allocation"></a>Alocação dinâmica de largura de banda

O Remote Desktop Protocol é um protocolo moderno projetado para se ajustar às condições de rede em mudança dinamicamente.
Em vez de utilizar os limites rígidos na utilização da largura de banda, o RDP utiliza uma deteção contínua de rede que monitoriza ativamente a largura de banda de rede disponível e o tempo de ida e volta do pacote. Com base nos resultados, o RDP seleciona dinamicamente as opções de codificação gráfica e atribui largura de banda para reorientação de dispositivos e outros canais virtuais.  
Esta tecnologia permite que o RDP utilize o tubo de rede completo quando disponível e recue rapidamente quando a rede é necessária para outra coisa.
O RDP deteta isso e ajusta algoritmos de qualidade de imagem, taxa de fotogramas ou compressão se outras aplicações solicitarem a rede.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Limite o uso da largura de banda da rede com a taxa de aceleração

Na maioria dos cenários, não há necessidade de limitar a utilização da largura de banda, uma vez que a limitação pode afetar a experiência do utilizador. No entanto, nas redes restritas, poderá querer limitar a utilização da rede. Outro exemplo é o arrendamento de redes que são cobradas pela quantidade de tráfego utilizado.

Nesses casos, pode limitar o tráfego de rede de saída rdp especificando uma taxa de aceleração na Política QoS.

  >[!NOTE]
  > [Certifique-se de que o Via Rápida RDP está ativado](./shortpath.md) - a limitação da taxa de aceleração não é suportada para o transporte de ligação inversa.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Implementar a taxa de aceleração limitando o anfitrião da sessão usando a Política de Grupo

Pode utilizar a Qualidade de Serviço (QoS) baseada em políticas no âmbito da Política de Grupo para definir a taxa de aceleração predefinida.

Para criar uma política QoS para anfitriões de sessão ligados ao domínio, em primeiro lugar, inscreva-se num computador no qual a Gestão de Políticas de Grupo foi instalada. Abrir a gestão da política do grupo (selecione Start, apontar para Ferramentas Administrativas e, em seguida, selecionar Gestão de Políticas de Grupo) e, em seguida, completar os seguintes passos:

1. Na Gestão da Política de Grupo, localize o contentor onde deve ser criada a nova política. Por exemplo, se todos os computadores anfitriões de sessão estiverem localizados num ou chamado **Session Hosts,** a nova política deve ser criada no Session Hosts OU.

2. Clique com o botão direito no recipiente apropriado e, em seguida, **selecione Criar um GPO neste domínio e ligá-lo aqui**.

3. Na nova caixa de diálogo **GPO,** digite um nome para o novo objeto de Política de Grupo na caixa **Nome** e, em seguida, selecione **OK**.

4. Clique com o direito na política recém-criada e, em seguida, **selecione Editar**.

5. No Editor de Gestão de Políticas de Grupo, expanda **a configuração do computador,** expanda **as definições do Windows,** clique com o botão direito **QoS** e, em seguida, selecione **Criar nova política**.

6. Na caixa de diálogo **QoS baseada em políticas,** na página de abertura, escreva um nome para a nova política na caixa **Nome.** **Selecione Especificar Taxa de Aceleração de Saída** e definir o valor necessário e, em seguida, selecione **Seguinte**.

7. Na página seguinte, selecione **Apenas aplicações com este nome executável** e introduza o nome **svchost.exe** , e, em seguida, selecione **Next**. Esta definição instrui a política para apenas priorizar o tráfego correspondente do Serviço de Ambiente de Trabalho Remoto.

8. Na terceira página, certifique-se de que tanto qualquer **endereço IP de origem** como **qualquer endereço IP de destino** estão selecionados. Selecione **Seguinte**. Estas duas definições garantem que os pacotes serão geridos independentemente do computador (endereço IP) que enviou os pacotes e que computador (endereço IP) receberá os pacotes.

9. Na página quatro, selecione **UDP** do **Select the protocol this QoS policy applie to** drop-down list.

10. Sob a posição **Especifique o número da porta de origem** , selecione **A partir desta porta de origem ou alcance**. Na caixa de texto que o acompanha, tipo **3390**. Selecione **Concluir**.

As novas políticas que criou não entrarão em vigor até que a Política de Grupo seja atualizada nos computadores anfitriões da sessão. Embora a Política de Grupo refresque periodicamente por si só, pode forçar uma atualização imediata seguindo estes passos:

1. Em cada anfitrião de sessão para o qual pretende atualizar a Política de Grupo, abra um Aviso de Comando como administrador *(Executar como administrador* ).

2. Na pronta do comando, insira

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Implementar a taxa de aceleração limitando no anfitrião da sessão usando o PowerShell

Pode definir a velocidade do acelerador para o Shortpath RDP utilizando o cmdlet PowerShell abaixo:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os requisitos de largura de banda para o Windows Virtual Desktop, consulte [os requisitos de largura de banda do Protocolo de Ambiente de Trabalho Remoto (RDP) para o Windows Virtual Desktop](rdp-bandwidth.md).
* Para saber mais sobre a conectividade da rede virtual do Windows Desktop, consulte [a conectividade da rede virtual de desktop do Windows.](network-connectivity.md)
* Para começar com a Qualidade de Serviço (QoS) para o Windows Virtual Desktop, consulte [implementar qualidade de serviço (QoS) para Windows Virtual Desktop](rdp-quality-of-service-qos.md).
