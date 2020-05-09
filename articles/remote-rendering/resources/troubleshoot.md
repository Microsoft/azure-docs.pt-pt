---
title: Resolução de problemas
description: Informações de resolução de problemas para renderização remota de Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: c1b807c6e4fa269ac2ab8d7eacd3ca1d4f81a1ca
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792620"
---
# <a name="troubleshoot"></a>Resolução de problemas

Esta página lista questões comuns que interferem com a Renderização Remota Azure, e formas de resolvê-las.

## <a name="client-cant-connect-to-server"></a>O cliente não pode ligar-se ao servidor

Certifique-se de que as suas firewalls (no dispositivo, dentro dos routers, etc.) não bloqueiam as seguintes portas:

* **50051 (TCP)** - necessário para a ligação inicial (aperto de mão HTTP)
* **8266 (TCP+UDP)** - necessário para a transferência de dados
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - necessário para [arrinspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Erro 'Desligado: VideoFormatNotDisponível'

Verifique se a sua GPU suporta a descodificação de vídeo de hardware. Ver [Desenvolvimento PC](../overview/system-requirements.md#development-pc).

Se estiver a trabalhar num portátil com duas GPUs, é possível que a GPU em que está a ser em execução por defeito, não forneça funcionalidade seletiva de descodificação de vídeo de hardware. Em caso afirmativo, tente forçar a sua aplicação a utilizar a outra GPU. Isto é muitas vezes possível nas configurações do condutor da GPU.

## <a name="h265-codec-not-available"></a>Código H265 não disponível

Existem duas razões pelas quais o servidor pode recusar-se a ligar-se a um erro **não disponível.**

**O código H265 não está instalado:**

Primeiro certifique-se de instalar as extensões de **vídeo HEVC** como mencionado na secção [software](../overview/system-requirements.md#software) dos requisitos do sistema.

Se ainda tiver problemas, certifique-se de que a sua placa gráfica suporta H265 e tem o mais recente controlador gráfico instalado. Consulte a secção [de Desenvolvimento PC](../overview/system-requirements.md#development-pc) dos requisitos do sistema para informações específicas do fornecedor.

**O codec está instalado, mas não pode ser usado:**

A razão para esta questão é uma definição de segurança incorreta nos DLLs. Este problema não se manifesta ao tentar ver vídeos codificados com H265. A reinstalação do codec também não resolve o problema. Em vez disso, execute os seguintes passos:

1. Abra um **PowerShell com direitos de administração** e corra

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Esse comando deve `InstallLocation` forrindo o do codec, algo como:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Abra essa pasta no Windows Explorer
1. Deve haver um **x86** e uma subpasta **x64.** Clique à direita numa das pastas e escolha **Propriedades**
    1. Selecione o separador **'Segurança'** e clique no botão de definições **avançadas**
    1. Clique em **Alterar** para o **Proprietário**
    1. Digitar **administradores** no campo de texto
    1. Clique em **Ver Nomes** e **OK**
1. Repita os passos acima para a outra pasta
1. Repita também os passos acima de cada ficheiro DLL dentro de ambas as pastas. Deve haver quatro DLLs no total.

Para verificar se as definições estão agora corretas, faça-o para cada um dos quatro DLLs:

1. **Selecione Propriedades > > De Segurança Editar**
1. Consulte a lista de todos os **Grupos /Utilizadores** e certifique-se de que cada um tem o conjunto direito **Read & Executar** (a marca de verificação na coluna de **espera** deve ser assinalada)

## <a name="low-video-quality"></a>Baixa qualidade de vídeo

A qualidade do vídeo pode ser comprometida quer pela qualidade da rede, quer pelo código de vídeo H265 em falta.

* Consulte os passos para [identificar problemas](#unstable-holograms)de rede .
* Consulte os [requisitos](../overview/system-requirements.md#development-pc) do sistema para instalar o controlador gráfico mais recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Vídeo gravado com MRC não reflete a qualidade da experiência ao vivo

Um vídeo pode ser gravado em Hololens através da [Mixed Reality Capture (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). No entanto, o vídeo resultante tem pior qualidade do que a experiência ao vivo por duas razões:
* O framerate de vídeo está limitado a 30 Hz em oposição a 60 Hz.
* As imagens de vídeo não passam pelo passo de processamento de [reprojeção](../overview/features/late-stage-reprojection.md) em fase tardia, pelo que o vídeo parece ser mais picador.

Ambos são limitações inerentes à técnica de gravação.

## <a name="black-screen-after-successful-model-loading"></a>Tela preta após carregamento de modelo bem sucedido

Se estiver ligado ao tempo de execução da renderização e tiver carregado um modelo com sucesso, mas só veja um ecrã preto depois, então isto pode ter algumas causas distintas.

Recomendamos testar as seguintes coisas antes de fazer uma análise mais aprofundada:

* O código H265 está instalado? Embora deva haver uma recaída no código H264, vimos casos em que este recuo não funcionou corretamente. Consulte os [requisitos](../overview/system-requirements.md#development-pc) do sistema para instalar o controlador gráfico mais recente.
* Ao utilizar um projeto de Unidade, feche a Unidade, elimine a *biblioteca* temporária e as pastas *obj* no diretório do projeto e carregue/construa novamente o projeto. Em alguns casos, os dados em cache fizeram com que a amostra não funcionasse corretamente sem razão óbvia.

Se estes dois passos não ajudaram, é necessário descobrir se os quadros de vídeo são ou não recebidos pelo cliente. Isto pode ser consultado programáticamente, como explicado no capítulo de [consultas de desempenho do lado do servidor.](../overview/features/performance-queries.md) O `FrameStatistics struct` tem um membro que indica quantos quadros de vídeo foram recebidos. Se este número for maior do que 0 e aumentar ao longo do tempo, o cliente recebe quadros de vídeo reais do servidor. Consequentemente, deve ser um problema do lado do cliente.

### <a name="common-client-side-issues"></a>Problemas comuns do lado do cliente

**O modelo excede os limites do VM selecionado, especificamente o número máximo de polígonos:**

Consulte [limitações específicas do tamanho](../reference/limits.md#overall-number-of-polygons)do VM .

**O modelo não está dentro da vista frustum:**

Em muitos casos, o modelo é apresentado corretamente, mas localizado fora do frustum da câmara. Uma razão comum é que o modelo foi exportado com um pivô muito fora do centro, por isso é cortado pelo avião de corte da câmara. Ajuda a consultar a caixa de delimitação do modelo programáticamente e visualizar a caixa com a Unidade como uma caixa de linha ou imprimir os seus valores para o registo de depuração.

Além disso, o processo de conversão gera um [ficheiro json de saída](../how-tos/conversion/get-information.md) ao lado do modelo convertido. Para depurar problemas de posicionamento do `boundingBox` modelo, vale a pena olhar para a entrada na [secção outputEstatísticas:](../how-tos/conversion/get-information.md#the-outputstatistics-section)

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

A caixa de delimitação é descrita como a `min` e `max` posição no espaço 3D, em metros. Assim, uma coordenada de 1000.0 significa que está a 1 km de distância da origem.

Pode haver dois problemas com esta caixa de delimitação que levam a geometria invisível:
* **A caixa pode estar muito fora do centro,** por isso o objeto é completamente cortado devido a um recorte de avião distante. Os `boundingBox` valores neste caso seriam `min = [-2000, -5,-5], max = [-1990, 5,5]`assim: usando uma grande compensação no eixo X como exemplo aqui. Para resolver este tipo de `recenterToOrigin` problema, ative a opção na [configuração](../how-tos/conversion/configure-model-conversion.md)de conversão do modelo .
* **A caixa pode ser centrada, mas ser ordens de magnitude demasiado grande.** Isto significa que, embora a câmara comece no centro do modelo, a sua geometria é cortada em todas as direções. Valores típicos `boundingBox` neste caso `min = [-1000,-1000,-1000], max = [1000,1000,1000]`seriam assim: . A razão para este tipo de problema é geralmente uma incompatibilidade de escala unitária. Para compensar, especifique um [valor de escala durante a conversão](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) ou marque o modelo de origem com as unidades corretas. A escala também pode ser aplicada no nó raiz ao carregar o modelo em tempo de execução.

**O oleoduto de renderização da Unidade não inclui os ganchos de renderização:**

Os ganchos de renderização remota Azure no pipeline de renderização da Unidade para fazer a composição da moldura com o vídeo e para fazer a reprojeção. Para verificar se estes ganchos existem, abra o menu *Janela > Análise > debugger Frame*. Ative-o e certifique-se de `HolographicRemotingCallbackPass` que existem duas entradas para o gasoduto:

![Debugger quadro de unidade](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Código de unidade usando a API de renderização remota não compila

### <a name="use-debug-when-compiling-for-unity-editor"></a>Use debug ao compilar para Editor de Unidade

Mude o tipo de *construção* da solução Deunidade para **Debug**. Ao testar arr no editor `UNITY_EDITOR` de Unidade, o definição só está disponível nas construções 'Debug'. Note que isto não está relacionado com o tipo de construção utilizado para [aplicações implantadas,](../quickstarts/deploy-to-hololens.md)onde deve preferir as construções 'Release'.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Compile falhas ao compilar amostras de unidade para HoloLens 2

Temos visto falhas espúrias ao tentar compilar amostras de Unidade (quickstart, ShowCaseApp, ..) para HoloLens 2. O Visual Studio queixa-se de não poder copiar alguns ficheiros, apesar de estarem lá. Se acertar neste problema:
* Remova todos os ficheiros de Unidade temporários do projeto e tente novamente.
* Certifique-se de que os projetos estão localizados num diretório no disco com um percurso razoavelmente curto, uma vez que o passo da cópia às vezes parece ter problemas com nomes de ficheiros longos.
* Se isso não ajudar, pode ser que a MS Sense interfira com o passo da cópia. Para estabelecer uma exceção, executar este comando de registo a partir da linha de comando (requer direitos de administração):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Hologramas instáveis

No caso de objetos renderizados parecerem mover-se juntamente com movimentos da cabeça, pode estar a encontrar problemas com a *Reprojection Late Stage* (LSR). Consulte a secção de [Reprojeção](../overview/features/late-stage-reprojection.md) de Fase Tardia para obter orientações sobre como abordar tal situação.

Outra razão para hologramas instáveis (oscilações, desasque, nervosismo ou hologramas saltitantes) pode ser a fraca conectividade da rede, em particular a largura de banda insuficiente da rede, ou a latência demasiado elevada. Um bom indicador para a qualidade da sua `ARRServiceStats.VideoFramesReused`ligação de rede é o valor das estatísticas de [desempenho.](../overview/features/performance-queries.md) Os quadros reutilizados indicam situações em que uma antiga moldura de vídeo precisava de ser reutilizada do lado do cliente porque não havia nova moldura de vídeo disponível – por exemplo, devido à perda de pacotes ou devido a variações na latência da rede. Se `ARRServiceStats.VideoFramesReused` for frequentemente maior do que zero, isto indica um problema de rede.

Outro valor a `ARRServiceStats.LatencyPoseToReceiveAvg`olhar é. Deve ser consistentemente inferior a 100 ms. Se vir valores mais elevados, isto indica que está ligado a um centro de dados que está muito longe.

Para obter uma lista de potenciais atenuações, consulte as [diretrizes para](../reference/network-requirements.md#guidelines-for-network-connectivity)a conectividade da rede.

## <a name="next-steps"></a>Passos seguintes

* [Requisitos de sistema](../overview/system-requirements.md)
* [Requisitos da rede](../reference/network-requirements.md)
