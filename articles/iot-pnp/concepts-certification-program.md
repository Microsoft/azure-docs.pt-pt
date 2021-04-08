---
title: Programa de dispositivos certificados Azure | Microsoft Docs
description: Compreenda o programa Azure Certified Device.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92048105"
---
# <a name="what-is-the-azure-certified-device-program"></a>O que é o programa Azure Certified Device?

O programa Azure Certified Device garante que as soluções do cliente funcionam perfeitamente com os seus serviços Azure. O programa utiliza ferramentas, serviços e um mercado para partilhar conhecimentos da indústria e boas práticas com uma comunidade de fabricantes de dispositivos e soluções.

Este programa foi concebido para:

- **Dê confiança aos clientes:** Os clientes podem comprar com confiança dispositivos certificados pela Microsoft para uso com serviços Azure.

- **Ajude os clientes a encontrar os dispositivos certos para a sua solução:** Os construtores de dispositivos podem publicar as capacidades únicas dos seus dispositivos como parte do processo de certificação. Os clientes podem facilmente encontrar os produtos que se adequam às suas necessidades.

- **Promover dispositivos certificados:** Os construtores de dispositivos obtêm maior visibilidade, contacto com os clientes e utilização da marca Azure Certified Device.

Este artigo descreve como:

- A bordo da sua empresa para o programa Azure Certified Device.
- Determine qual a certificação aplicável para o seu dispositivo.
- Encontre os requisitos do programa e outros recursos para começar a testar.
- Utilize o portal Azure Certified Device para validar o seu dispositivo.

## <a name="onboarding"></a>Inclusão

Para certificar os seus dispositivos no [portal Azure Certified Device,](https://aka.ms/acdp)tem de completar os seguintes passos:

1. Certifique-se de que a sua empresa tem uma conta Azure Ative Directory utilizando um trabalho ou inquilino escolar.
2. Junte-se à [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) utilizando a sua conta.
3. Inscreva-se no portal do [Dispositivo Certificado Azure](https://aka.ms/acdp) depois de se ter juntado à MPN.
4. Reveja e assine o acordo do [programa](https://aka.ms/acdagreement) na página de perfil da empresa

### <a name="company-profile"></a>Perfil da empresa

Para gerir o perfil da sua empresa no portal Azure Certified Device, selecione **perfil da Empresa**. O perfil da empresa inclui o URL da empresa, endereço de e-mail e logotipo. Aceite o acordo de programa nesta página antes de continuar com quaisquer operações de certificação.

A página de descrição do dispositivo no catálogo de Dispositivos Certificados Azure utiliza as informações do perfil da empresa.

## <a name="choose-the-certification"></a>Escolha a certificação

Existem três certificações diferentes, cada uma focada em entregar um valor diferente ao cliente. Dependendo do tipo de dispositivo que está a construir e do seu público-alvo, pode escolher a certificação ou certificações mais aplicáveis:

### <a name="azure-certified-device"></a>Dispositivo Certificado Azure

_A certificação Azure Certified Device_ valida que um dispositivo pode ligar-se ao Azure IoT Hub e providenciar de forma segura através do Serviço de Provisionamento de Dispositivos (DPS). Esta certificação reflete a funcionalidade e interoperabilidade de um dispositivo que é uma base necessária para certificações mais avançadas.

- Para saber mais, consulte os [requisitos de certificação.](https://aka.ms/acdrequirements)
- Para saber mais sobre a utilização de DPS para ligar o seu dispositivo ao Azure IoT Hub, consulte a visão geral dos [dispositivos de provisionamento](../iot-dps/about-iot-dps.md).

### <a name="iot-plug-and-play"></a>IoT Plug and Play

_A certificação IoT Plug and Play_, uma certificação incremental para a certificação Azure Certified Device, simplifica o processo de construção de dispositivos sem código de dispositivo personalizado. O IoT Plug and Play permite aos parceiros de hardware construir dispositivos que facilmente se integrem com soluções cloud baseadas em soluções Azure IoT Central e de terceiros.

- Para saber mais, consulte os [requisitos de certificação.](https://aka.ms/acdiotpnprequirements)
- Para saber mais sobre como preparar um dispositivo para testes IoT Plug e Play, consulte [como certificar os dispositivos IoT Plug e Play](howto-certify-device.md).

### <a name="edge-managed"></a>Edge gerido

_A certificação Edge Managed_, uma certificação incremental para a certificação Azure Certified Device, foca-se nos padrões de gestão de dispositivos para dispositivos Azure IoT que executam Windows, Linux ou RTOS. Atualmente, esta certificação de programa centra-se na compatibilidade de tempo de execução edge para implementação e gestão de módulos.

> [!TIP]
> Este programa era anteriormente conhecido como o _programa de certificação IoT Edge._

- Para saber mais, consulte os [requisitos de certificação.](https://aka.ms/acdedgemanagedrequirements)
- Para saber mais sobre ioT Edge, consulte [a visão geral do IoT Edge](../iot-edge/about-iot-edge.md).
- Para saber mais sobre sistemas operativos e contentores suportados, consulte [os sistemas suportados ioT Edge](../iot-edge/support.md).

## <a name="use-the-azure-certified-device-portal"></a>Utilize o portal de dispositivos certificados Azure

Esta secção resume como utilizar o [portal Azure Certified Device](https://certify.azure.com). Para saber mais, consulte o [Início com o guia do portal.](https://aka.ms/acdhelp)

Para certificar um dispositivo no programa Azure Certified Device, complete os seguintes quatro passos:

1. Fornecer os detalhes do dispositivo
2. Teste o dispositivo
3. Submeter e completar a revisão
4. Publique no catálogo de dispositivos certificados Azure (opcional)

### <a name="provide-device-details"></a>Fornecer detalhes do dispositivo

Para cada dispositivo que pretende certificar, utilize os formulários no portal de certificação para registar detalhes sobre o hardware do dispositivo, instruções de configuração e material de marketing:

- **Informações do dispositivo:** Recolhe informações sobre o dispositivo, como o seu nome, descrição, detalhes do hardware e sistema operativo.
- **Envie um guia em** PDF : Um documento PDF que um cliente pode usar para utilizar rapidamente o seu produto. [Os modelos de amostra](https://aka.ms/GSTemplate) estão disponíveis.
- **Detalhes de marketing:** Forneça informações de marketing prontas para o cliente para o seu dispositivo, como uma informação de imagem e distribuidor.
- **Certificações adicionais da indústria:** Uma secção opcional que lhe permite fornecer informações sobre quaisquer outras certificações que o dispositivo tenha.

### <a name="test-the-device"></a>Teste o dispositivo

Esta fase interage com o seu dispositivo e executa uma série de testes após o dispositivo utilizar DPS para ligar ao IoT Hub. Após a conclusão, pode visualizar um conjunto de ficheiros de registo com os resultados dos testes do seu dispositivo.

O portal de certificação tem instruções sobre como ligar-se à instância IoT Hub utilizada para testes. Pode estabelecer a ligação DPS através de qualquer um dos [métodos de atestado suportados](../iot-dps/concepts-service.md#attestation-mechanism).

A equipa do Dispositivo Certificado Azure pode contactar o construtor do dispositivo para posterior validação manual do dispositivo.

### <a name="submit-and-publish"></a>Submeter e publicar

A fase final exigida é submeter o projeto para revisão. Este passo notifica um membro da equipa de Dispositivos Certificados Azure para rever o seu projeto para a completude, incluindo os detalhes do dispositivo e marketing, e o guia get-start. Um membro da equipa pode contactá-lo no endereço de e-mail da empresa previamente fornecido com perguntas ou editar pedidos antes da aprovação.

Se o seu dispositivo necessitar de mais validação manual como parte da certificação, receberá um aviso neste momento.

Quando um dispositivo é certificado, pode optar por publicar os seus dados do produto no Catálogo de Dispositivos Certificados Azure utilizando a **Funcionalidade De Publicação para catalogar** funcionalidades na página de resumo do produto.

## <a name="if-you-have-questions"></a>Se tiver perguntas

Contacte a equipa [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question) se tiver alguma dúvida sobre os programas de certificação, o portal de certificação ou o Catálogo de Dispositivos Certificados Azure.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral do programa Azure Certified Device, um próximo passo sugerido é aprender [como certificar os dispositivos IoT Plug e Play](howto-certify-device.md).