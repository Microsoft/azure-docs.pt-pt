---
title: Requisitos de sistema
description: Lista os requisitos do sistema para renderização remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 536f5e7d2fa12d1f9a9f09a31b463d2096dcad37
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207415"
---
# <a name="system-requirements"></a>Requisitos de sistema

> [!IMPORTANT]
> **A Azure Remote Rendering** está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este capítulo enumera os requisitos mínimos do sistema para trabalhar com *a renderização remota Azure* (ARR).

## <a name="development-pc"></a>Desenvolvimento PC

* Versão 10 do Windows 1903 ou superior.
* Condutores gráficos atualizados.
* Opcional: [Descodificador de vídeo de hardware H265,](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)se quiser utilizar a pré-visualização local de conteúdos renderizados remotamente (por exemplo, em Unidade).

> [!IMPORTANT]
> A atualização do Windows nem sempre fornece os mais recentes controladores da GPU, consulte o site do fabricante da GPU para obter os mais recentes condutores:
>
> * [Motoristas da AMD](https://www.amd.com/en/support)
> * [Condutores de informação](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Condutores da NVIDIA](https://www.nvidia.com/Download/index.aspx)

A tabela abaixo lista quais as GPUs suportam a descodagem de hardware H265.

| Fabricante de GPU | Modelos suportados |
|-----------|:-----------|
| NVIDIA | Verifique a Matriz de **Suporte NVDEC** [na parte inferior desta página](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). A sua GPU precisa de um SIM na coluna **H.265 4:2:0 8-bit.** |
| AMD | GPUs com pelo menos a versão 6 do [descodificador](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)de vídeo unificado da AMD . |
| Informação | Skylake e novos CPUs |

Mesmo que o código H265 correto possa ser instalado, as propriedades de segurança nos DLLs codec podem causar falhas de inicialização de codec. O [guia de resolução de problemas](../resources/troubleshoot.md#h265-codec-not-available) descreve medidas como resolver este problema. O problema DLL só pode ocorrer quando se utiliza o serviço numa aplicação de ambiente de trabalho, por exemplo na Unidade.

## <a name="devices"></a>Dispositivos

A Azure Remote Rendering suporta atualmente apenas **o holoLens 2** e o windows desktop como um dispositivo alvo. Consulte a secção [de limitações](../reference/limits.md#platform-limitations) da plataforma.

É importante usar o mais recente codec HEVC, uma vez que as versões mais recentes têm melhorias significativas na latência. Para verificar qual a versão instalada no seu dispositivo:

1. Inicie a **Microsoft Store**.
1. Clique no botão **"..."** no topo direito.
1. Selecione **Downloads e Atualizações**.
1. Pesse na lista de **extensões de vídeo HEVC do Fabricante do Dispositivo**. Se este item não estiver listado em atualizações, a versão mais recente já está instalada.
1. Certifique-se de que o codec listado tem pelo menos a versão **1.0.21821.0**.
1. Clique no botão **Get Updates** e aguarde a sua instalação.

## <a name="network"></a>Rede

Uma ligação de rede estável e de baixa latência é fundamental para uma boa experiência do utilizador.

Consulte o capítulo dedicado para [os requisitos da rede.](../reference/network-requirements.md)

Para resolver problemas de rede, consulte o [Guia de Resolução de Problemas](../resources/troubleshoot.md#unstable-holograms).

### <a name="network-ports"></a>Portas de rede

Certifique-se de que as suas firewalls (no dispositivo, no interior dos routers, etc.) não bloqueiem as seguintes portas:

| Porta              | Protocolo | Permitir    | Descrição |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Saída | Ligação inicial (aperto de mão HTTP) |
| 8266              | UDP      | Saída | Transferência de dados |
| 5000, 5433, 8443  | TCP      | Saída | Requerido para [a ferramenta ArrInspector](../resources/tools/arr-inspector.md)|


## <a name="software"></a>Software

Deve ser instalado o seguinte software:

* A mais recente versão do **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Ferramentas de Estúdio Visual para Realidade Mista.](/windows/mixed-reality/install-the-tools) Especificamente, as seguintes instalações *de carga de trabalho* são obrigatórias:
  * **Desenvolvimento de desktop com C++**
  * **Desenvolvimento da Plataforma Universal windows (UWP)**
* **Windows SDK 10.0.18362.0** [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(download)](https://git-scm.com/downloads)
* Opcional: Para visualizar o fluxo de vídeo do servidor num PC de secretária, precisa das **extensões de vídeo HEVC** [(link Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Certifique-se de que a versão mais recente é instalada, verificando as atualizações na loja.

## <a name="unity"></a>Unity

Para o desenvolvimento com Unidade, instale

* Unidade 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
* Instale estes módulos na Unidade:
  * **UWP** - Suporte universal de construção de plataformas windows
  * **IL2CPP** - Suporte à Construção de Janelas (IL2CPP)

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Renderiza um modelo com Unidade](../quickstarts/render-model.md)