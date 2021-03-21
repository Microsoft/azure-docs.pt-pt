---
title: Resolução de problemas
description: Informações de resolução de problemas para renderização remota de Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4990f0d0a10709f2c1c5a17806020cd685f999fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593338"
---
# <a name="troubleshoot"></a>Resolução de problemas

Esta página lista questões comuns que interferem com a renderização remota do Azure e formas de resolvê-las.

## <a name="cant-link-storage-account-to-arr-account"></a>Não é possível ligar a conta de armazenamento à conta ARR

Por vezes, durante [a ligação de uma conta de armazenamento,](../how-tos/create-an-account.md#link-storage-accounts) a conta de renderização remota não está listada. Para corrigir este problema, aceda à conta ARR no portal Azure e selecione **Identidade** no grupo **Definições** à esquerda. Certifique-se de que **o estado** está definido para **On**.
![Depurador de quadro de unidade](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>O cliente não consegue ligar-se ao servidor

Certifique-se de que as suas firewalls (no dispositivo, no interior dos routers, etc.) não bloqueiem as portas mencionadas nos [requisitos](../overview/system-requirements.md#network-firewall)do Sistema .

## <a name="error-disconnected-videoformatnotavailable"></a>Erro `Disconnected: VideoFormatNotAvailable` ' '

Verifique se a sua GPU suporta a descodificamento de vídeo de hardware. Ver [Desenvolvimento PC](../overview/system-requirements.md#development-pc).

Se estiver a trabalhar num portátil com duas GPUs, é possível que a GPU em que esteja a funcionar por defeito não forneça funcionalidades de descodagem de vídeo de hardware. Em caso afirmativo, tente forçar a sua aplicação a utilizar a outra GPU. Isto é frequentemente possível nas definições do condutor da GPU.

## <a name="retrieve-sessionconversion-status-fails"></a>Falha no estado de sessão/conversão da sessão de recuperação

Enviar comandos REST API com demasiada frequência fará com que o servidor se ausse e volte a falhar eventualmente. O código de estado http no caso do estrangulamento é de 429 ("demasiados pedidos"). Em regra, deve haver um atraso de **5-10 segundos entre chamadas subsequentes**.

Note que este limite não afeta apenas as chamadas de API rest quando chamadas diretamente, mas também as suas congéneres C#/C++, tais `Session.GetPropertiesAsync` `Session.RenewAsync` como, ou `Frontend.GetAssetConversionStatusAsync` .

Se experimentar estrangulamento do lado do servidor, altere o código para fazer as chamadas com menos frequência. O servidor reinicia o estado de estrangulamento a cada minuto, pelo que é seguro voltar a ser reiniciado o código após um minuto.

## <a name="h265-codec-not-available"></a>Código H265 não disponível

Existem duas razões pelas quais o servidor pode recusar-se a ligar-se a um `codec not available` erro.

**O codec H265 não está instalado:**

Primeiro certifique-se de instalar as **Extensões de Vídeo HEVC,** conforme mencionado na secção [de Software](../overview/system-requirements.md#software) dos requisitos do sistema.

Se ainda tiver problemas, certifique-se de que a sua placa gráfica suporta H265 e tem o mais recente controlador gráfico instalado. Consulte a secção [de PC de Desenvolvimento](../overview/system-requirements.md#development-pc) dos requisitos do sistema para informações específicas do fornecedor.

**O codec está instalado, mas não pode ser usado:**

A razão para esta questão é uma definição de segurança incorreta nos DLLs. Este problema não se manifesta quando se tenta ver vídeos codificados com H265. Reinstalar o codec também não resolve o problema. Em vez disso, execute os seguintes passos:

1. Abra um **PowerShell com direitos de administração** e corra

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Este comando deve dar o `InstallLocation` código, algo como:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Abra a pasta no Windows Explorer
1. Deve haver um **x86** e um **sub-14.** Clique com o direito numa das pastas e escolha **Propriedades**
    1. Selecione o separador **Segurança** e clique no botão **Definições Avançadas**
    1. Clique em **Alterar** para o **Proprietário**
    1. Tipo **de Administradores** no campo de texto
    1. Clique **em Verificar Nomes** e **OK**
1. Repita os passos acima para a outra pasta
1. Repita também os passos acima em cada ficheiro DLL dentro de ambas as pastas. Deve haver quatro DLLs no total.

Para verificar se as definições estão agora corretas, faça-o para cada um dos quatro DLLs:

1. Selecione **propriedades > > de segurança Editar**
1. Consulte a lista de todos os **Grupos / Utilizadores** e certifique-se de que cada um tem o conjunto de **leitura & Executar** o conjunto certo (a marca de verificação na coluna de **permitir** deve ser assinalada)

## <a name="low-video-quality"></a>Baixa qualidade de vídeo

A qualidade do vídeo pode ser comprometida quer pela qualidade da rede, quer pelo código de vídeo H265 em falta.

* Consulte as etapas para [identificar problemas de rede.](#unstable-holograms)
* Consulte os [requisitos](../overview/system-requirements.md#development-pc) do sistema para instalar o mais recente controlador gráfico.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Vídeo gravado com MRC não reflete a qualidade da experiência ao vivo

Um vídeo pode ser gravado em HoloLens através [de Mixed Reality Capture (MRC)](/windows/mixed-reality/mixed-reality-capture-for-developers). No entanto, o vídeo resultante tem pior qualidade do que a experiência ao vivo por duas razões:
* O framerate de vídeo é limitado a 30 Hz em oposição a 60 Hz.
* As imagens de vídeo não passam pelo passo de processamento de [reprojecção do estágio tardio,](../overview/features/late-stage-reprojection.md) pelo que o vídeo parece ser mais picante.

Ambos são limitações inerentes à técnica de gravação.

## <a name="black-screen-after-successful-model-loading"></a>Tela preta após carregamento de modelo bem-sucedido

Se estiver ligado ao tempo de execução de renderização e tiver carregado um modelo com sucesso, mas só vir uma tela preta depois, então isto pode ter algumas causas distintas.

Recomendamos testar as seguintes coisas antes de fazer uma análise mais aprofundada:

* O codec H265 está instalado? Embora deva haver uma recaída para o codec H264, temos visto casos em que este recuo não funcionou corretamente. Consulte os [requisitos](../overview/system-requirements.md#development-pc) do sistema para instalar o mais recente controlador gráfico.
* Ao utilizar um projeto De unidade, feche a Unidade, elimine as *pastas* temporárias da biblioteca e *do obj* no diretório do projeto e volte a carregar/construir o projeto. Em alguns casos, os dados em cache fizeram com que a amostra não funcionasse corretamente sem razão óbvia.

Se estes dois passos não ajudaram, é necessário descobrir se os quadros de vídeo são recebidos pelo cliente ou não. Isto pode ser consultado programáticamente como explicado no capítulo das consultas de desempenho do lado do [servidor.](../overview/features/performance-queries.md) O `FrameStatistics struct` tem um membro que indica quantos quadros de vídeo foram recebidos. Se este número for maior do que 0 e aumentando ao longo do tempo, o cliente recebe quadros de vídeo reais do servidor. Consequentemente, deve ser um problema do lado do cliente.

### <a name="common-client-side-issues"></a>Questões comuns do lado do cliente

**O modelo excede os limites do VM selecionado, especificamente o número máximo de polígonos:**

Consulte [limites específicos de tamanho do servidor](../reference/limits.md#overall-number-of-polygons).

**O modelo não está dentro da câmara frustum:**

Em muitos casos, o modelo é apresentado corretamente, mas localizado fora da câmara frustum. Uma razão comum é que o modelo foi exportado com um pivô distante do centro, por isso é cortado pelo plano de corte distante da câmara. Ajuda a consultar a caixa de deliminação do modelo programáticamente e visualizar a caixa com a Unidade como uma caixa de linha ou imprimir os seus valores ao registo de depuração.

Além disso, o processo de conversão gera um [ficheiro json de saída](../how-tos/conversion/get-information.md) juntamente com o modelo convertido. Para depurar problemas de posicionamento do modelo, vale a pena olhar para a `boundingBox` entrada na secção de [resultadosStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

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

A caixa de delimitação é descrita como uma `min` e `max` posição no espaço 3D, em metros. Uma coordenada de 1000.0 significa que está a 1 km da origem.

Pode haver dois problemas com esta caixa de delimitação que levam à geometria invisível:
* **A caixa pode estar muito longe do centro,** por isso o objeto é completamente cortado devido ao corte do plano distante. Os `boundingBox` valores neste caso seriam assim: `min = [-2000, -5,-5], max = [-1990, 5,5]` , usando uma grande compensação no eixo x como exemplo aqui. Para resolver este tipo de problema, ative a `recenterToOrigin` opção na [configuração](../how-tos/conversion/configure-model-conversion.md)de conversão do modelo .
* **A caixa pode ser centrada, mas ser ordens de grandeza demasiado grandes.** Isto significa que, embora a câmara comece no centro do modelo, a sua geometria é cortada em todas as direções. Valores `boundingBox` típicos neste caso seriam assim: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . A razão para este tipo de problema é geralmente uma incompatibilidade da escala unitária. Para compensar, especifique um [valor de escala durante a conversão](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) ou marque o modelo de origem com as unidades corretas. A escala também pode ser aplicada no nó de raiz ao carregar o modelo em tempo de execução.

**O gasoduto de renderização da Unidade não inclui os ganchos de renderização:**

Azure Remote Rendering ganchos no pipeline De unidade para fazer a composição da moldura com o vídeo, e para fazer a reprojecção. Para verificar se existem estes ganchos, abra o menu *:::no-loc text="Window > Analysis > Frame debugger":::* . Assegure-o e certifique-se de que existem duas entradas para `HolographicRemotingCallbackPass` o pipeline:

![Gasoduto de renderização de unidade](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>O padrão de tabuleiro de xadrez é renderizado após o carregamento do modelo

Se a imagem renderizada se parece com esta: ![ o Screenshot mostra uma grelha de quadrados pretos e brancos com um menu Tools.](../reference/media/checkerboard.png)
em seguida, o renderizador atinge os limites do [polígono para o tamanho padrão de configuração](../reference/vm-sizes.md). Para atenuar, mude para o tamanho de configuração **premium** ou reduza o número de polígonos visíveis.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>A imagem renderizada na Unidade está de cabeça para baixo

Certifique-se de seguir o [Tutorial de Unidade: Ver exatamente os modelos remotos.](../tutorials/unity/view-remote-models/view-remote-models.md) Uma imagem de cabeça para baixo indica que a Unidade é necessária para criar um alvo de renderização fora do ecrã. Este comportamento não é atualmente suportado e cria um enorme impacto de desempenho no HoloLens 2.

As razões para esta edição podem ser MSAA, HDR ou habilitar o processamento pós-processamento. Certifique-se de que o perfil de baixa qualidade é selecionado e definido como padrão na Unidade. Para isso vá a *Editar > Definições de Projeto... > Qualidade*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Código de unidade usando a API de renderização remota não compila

### <a name="use-debug-when-compiling-for-unity-editor"></a>Use o Debug ao compilar para o Editor de Unidade

Mude o tipo de *construção* da solução Unidade para **Debug**. Ao testar ARR no editor da Unidade, o define `UNITY_EDITOR` só está disponível nas construções 'Debug'. Note que este não está relacionado com o tipo de construção utilizado para [aplicações implementadas,](../quickstarts/deploy-to-hololens.md)onde deve preferir as construções de 'Lançamento'.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Compilar falhas ao compilar amostras de Unidade para HoloLens 2

Temos visto falhas espúrias ao tentar compilar amostras de Unidade (quickstart, ShowCaseApp, ..) para HoloLens 2. O Visual Studio queixa-se de não poder copiar alguns ficheiros, embora estejam lá. Se resolver este problema:
* Remova todos os ficheiros temporários da Unidade do projeto e tente novamente. Ou seja, fechar a Unidade, apagar as *pastas* temporárias da biblioteca e *do obj* no diretório do projeto e carregar/construir novamente o projeto.
* Certifique-se de que os projetos estão localizados num diretório em disco com um caminho razoavelmente curto, uma vez que o passo de cópia às vezes parece ter problemas com os longos filenames.
* Se isso não ajudar, pode ser que a MS Sense interfira com o passo de cópia. Para estabelecer uma exceção, executar este comando de registo a partir da linha de comando (requer direitos de administração):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Arm64 constrói projetos de unidade falham porque falta AudioPluginMsHRTF.dll

O `AudioPluginMsHRTF.dll` para Arm64 foi adicionado ao pacote *de Realidade Mista* do Windows *(com.unity.xr.windowsmr.metro)* na versão 3.0.1. Certifique-se de que tem a versão 3.0.1 ou posteriormente instalada através do Gestor de Pacotes de Unidade. A partir da barra de menus Unidade, navegue até *window > Package Manager* e procure o pacote de realidade mista do *Windows.*

## <a name="native-c-based-application-does-not-compile"></a>Aplicação baseada em C++ nativa não compila

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Erro de 'Biblioteca não encontrada' para aplicação UWP ou Dll

Dentro do pacote C++ NuGet, existe um ficheiro de ficheiro `microsoft.azure.remoterendering.Cpp.targets` que define qual do sabor binário deve ser utilizado. Para `UWP` identificar, as condições no ficheiro de verificação `ApplicationType == 'Windows Store'` . Por conseguinte, é necessário assegurar que este tipo se insegure no projeto. Este deve ser o caso ao criar uma aplicação UWP ou Dll através do assistente de projeto do Visual Studio.

## <a name="unstable-holograms"></a>Hologramas instáveis

No caso de os objetos renderizados parecerem estar a mover-se juntamente com os movimentos da cabeça, pode estar a deparar-se com problemas com *a Reprojecção do Estágio Tardio* (LSR). Consulte a secção de [Reprojecção do Estágio Tardio](../overview/features/late-stage-reprojection.md) para obter orientações sobre como abordar tal situação.

Outra razão para hologramas instáveis (oscilantes, deformas, nervosismo ou hologramas de salto) pode ser a fraca conectividade da rede, em particular a largura de banda de rede insuficiente, ou uma latência demasiado alta. Um bom indicador para a qualidade da sua ligação de rede é o valor das [estatísticas de desempenho.](../overview/features/performance-queries.md) `ServiceStatistics.VideoFramesReused` Os quadros reutilizados indicam situações em que uma antiga moldura de vídeo precisava de ser reutilizada do lado do cliente porque não havia uma nova moldura de vídeo disponível – por exemplo, devido à perda de pacotes ou devido a variações na latência da rede. Se `ServiceStatistics.VideoFramesReused` for frequentemente maior do que zero, isto indica um problema de rede.

Outro valor a olhar `ServiceStatistics.LatencyPoseToReceiveAvg` é. Deve ser consistentemente abaixo dos 100 ms. Ver valores mais elevados pode indicar que está ligado a um centro de dados que está muito longe.

Para obter uma lista de potenciais mitigações, consulte as diretrizes para a [conectividade da rede.](../reference/network-requirements.md#guidelines-for-network-connectivity)

## <a name="z-fighting"></a>Luta z

Enquanto o ARR oferece [uma funcionalidade de mitigação de combate a z,](../overview/features/z-fighting-mitigation.md)o z-fighting ainda pode aparecer no local. Este guia visa resolver estes problemas restantes.

### <a name="recommended-steps"></a>Passos recomendados

Utilize o seguinte fluxo de trabalho para mitigar a luta z:

1. Teste a cena com as definições padrão de ARR (mitigação de luta z em)

1. Desativar a mitigação do combate ao z através da sua [API](../overview/features/z-fighting-mitigation.md) 

1. Mude a câmara perto e longe do plano para um alcance mais próximo

1. Resolução de problemas na cena através da próxima secção

### <a name="investigating-remaining-z-fighting"></a>Investigando a restante luta z

Se os passos acima referidos foram esgotados e os restantes combates z são inaceitáveis, a causa subjacente ao combate ao z tem de ser investigada. Como indicado na [página de mitigação de z-fighting,](../overview/features/z-fighting-mitigation.md)existem duas razões principais para o combate z: perda de precisão de profundidade na extremidade da gama de profundidade, e superfícies que se cruzam enquanto são coplanares. A perda de precisão de profundidade é uma eventualidade matemática e só pode ser atenuada seguindo o passo 3 acima. As superfícies coplanares indicam uma falha do ativo de origem e são melhor corrigidas nos dados de origem.

O ARR tem uma característica para determinar se as superfícies podem ser z-fight: [Checkerboard realçando](../overview/features/z-fighting-mitigation.md). Também pode determinar visualmente o que causa o combate z. A primeira animação que se segue mostra um exemplo de perda de profundidade e precisão na distância, e a segunda mostra um exemplo de superfícies quase coplanares:

![A animação mostra um exemplo de perda de precisão de profundidade na distância.](./media/depth-precision-z-fighting.gif)  ![A animação mostra um exemplo de superfícies quase coplanares.](./media/coplanar-z-fighting.gif)

Compare estes exemplos com o seu z-fighting para determinar a causa ou seguir opcionalmente este fluxo de trabalho passo a passo:

1. Posicione a câmara acima das superfícies de combate z para olhar diretamente para a superfície.
1. Mova lentamente a câmara para trás, para longe das superfícies.
1. Se o z-fighting é visível todo o tempo, as superfícies são perfeitamente coplanares. 
1. Se o z-fighting é visível a maior parte do tempo, as superfícies são quase coplanares.
1. Se o z-fighting só é visível de longe, a causa é a falta de precisão de profundidade.

Superfícies coplanares podem ter uma série de causas diferentes:

* Um objeto foi duplicado pela aplicação de exportação devido a um erro ou a diferentes abordagens de fluxo de trabalho.

    Verifique estes problemas com o respetivo suporte de candidatura e candidatura.

* As superfícies são duplicadas e viradas para parecer em dupla face em renderizadores que usam o abate frontal ou traseiro.

    A importação através da conversão do [modelo](../how-tos/conversion/model-conversion.md) determina a principal sideritude do modelo. A dupla lateral é assumida como o padrão. A superfície será transformada como uma parede fina com iluminação fisicamente correta de ambos os lados. A unilateralidade pode ser implícita por bandeiras no ativo de origem, ou explicitamente forçada durante a conversão do [modelo](../how-tos/conversion/model-conversion.md). Adicionalmente, mas opcionalmente, o [modo unilateral único](../overview/features/single-sided-rendering.md) pode ser definido como "normal".

* Os objetos cruzam-se nos ativos de origem.

     Objetos transformados de uma forma que algumas das suas superfícies se sobrepõem também criam luta z. Transformar partes da árvore de cena na cena importada em ARR também pode criar este problema.

* As superfícies são propositadamente da autoria do toque, como decalques ou texto nas paredes.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Artefactos gráficos usando renderização estéreo multi-pass em aplicativos C++ nativos

Em alguns casos, as aplicações nativas personalizadas C++ que utilizam um modo de renderização estéreo multi-pass para conteúdo local (renderização para o olho esquerdo e direito em passes separados) depois de ligar para [**o BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) podem desencadear um bug do condutor. O inseto resulta em falhas de rasterização não determinísticas, fazendo com que triângulos individuais ou partes de triângulos do conteúdo local desapareçam aleatoriamente. Por razões de desempenho, recomenda-se, de qualquer forma, tornar o conteúdo local com uma técnica de renderização estéreo de passe único mais moderna, por exemplo, utilizando **SV_RenderTargetArrayIndex**.

## <a name="next-steps"></a>Passos seguintes

* [Requisitos de sistema](../overview/system-requirements.md)
* [Requisitos de rede](../reference/network-requirements.md)