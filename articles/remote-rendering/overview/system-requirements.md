---
title: Requisitos de sistema
description: Lista os requisitos do sistema para a renderização remota azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: c239f7062dc39492a0cf63ac3aadbaf94acbf032
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680443"
---
# <a name="system-requirements"></a>Requisitos de sistema

> [!IMPORTANT]
> **A Renderização Remota Azure** está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Este capítulo enumera os requisitos mínimos do sistema para trabalhar com a *Renderização Remota Azure* (ARR).

## <a name="development-pc"></a>PC de desenvolvimento

* Windows 10 versão 1903 ou superior.
* Condutores gráficos atualizados.
* Opcional: Descodificador de vídeo de hardware H265, se pretender utilizar a pré-visualização local de conteúdo renderizado remotamente (por exemplo, em Unidade).

> [!IMPORTANT]
> A atualização do Windows nem sempre fornece os mais recentes controladores GPU, consulte o site do fabricante da GPU para obter os mais recentes condutores:
>
> * [Condutores amd](https://www.amd.com/en/support)
> * [Motoristas de informação](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Condutores da NVIDIA](https://www.nvidia.com/Download/index.aspx)

A tabela abaixo lista quais AS GPUs suportam a descodificação de vídeo de hardware H265.

| Fabricante de GPU | Modelos suportados |
|-----------|:-----------|
| NVIDIA | Verifique a Matriz de **Suporte NVDEC** [na parte inferior desta página](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). A sua GPU precisa de um SIM na coluna **H.265 4:2:0 8-bit.** |
| AMD | GPUs com pelo menos a versão 6 do [Descodificador de Vídeo Unificado](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)da AMD . |
| Informação | Skylake e cpUs mais recentes |

Mesmo que o código H265 correto possa ser instalado, as propriedades de segurança nos DLLs codec podem causar falhas de inicialização codec. O [guia de resolução](../resources/troubleshoot.md#h265-codec-not-available) de problemas descreve passos como resolver este problema. O problema dLL só pode ocorrer quando se utiliza o serviço numa aplicação de ambiente de trabalho, por exemplo em Unidade.

## <a name="devices"></a>Dispositivos

A tualmente, a Renderização Remota Azure suporta apenas **holoLens 2** e Windows desktop UWP como um dispositivo-alvo.

É importante usar o mais recente codec HEVC, uma vez que as versões mais recentes têm melhorias significativas na latência. Para verificar qual a versão instalada no seu dispositivo:

1. Inicie a **Microsoft Store**.
1. Clique no botão **"..."** no topo direito.
1. Selecione **Downloads e Atualizações**.
1. Pesquise na lista as **extensões de vídeo HEVC do Fabricante do Dispositivo**.
1. Certifique-se de que o codec listado tem pelo menos a versão **1.0.21821.0**.
1. Clique no botão **'Obter Actualizações'** e aguarde a sua instalação.

## <a name="network"></a>Rede

Uma ligação estável e de baixa latência é fundamental para uma boa experiência do utilizador.

Consulte o capítulo dedicado para [obter requisitos](../reference/network-requirements.md)de rede .

Para problemas de rede de resolução de problemas, consulte o [Guia de Resolução](../resources/troubleshoot.md#unstable-holograms)de Problemas .

## <a name="software"></a>Software

Deve ser instalado o seguinte software:

* A versão mais recente do **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0** [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(download)](https://git-scm.com/downloads)
* Opcional: Para visualizar o fluxo de vídeo do servidor num PC de ambiente de trabalho, precisa das extensões de **vídeo HEVC** [(link Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7).

## <a name="unity"></a>Unidade

Para desenvolvimento com unidade, instale

* Unidade 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
* Instale estes módulos na Unidade:
  * **UWP** - Suporte universal para construir plataforma Windows
  * **IL2CPP** - Suporte para construção de janelas (IL2CPP)

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Render um modelo com Unidade](../quickstarts/render-model.md)
