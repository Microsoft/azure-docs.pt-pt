---
title: Lista de verificação de pré-certificação para ofertas de módulo IoT Edge no Azure Marketplace
description: Conheça os requisitos específicos de certificação para a publicação de módulos IoT Edge no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 31c19f62f0328fca05562eaa2f19b7a79c0f3e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562703"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Lista de verificação de pré-certificação para módulos IoT Edge

> [!NOTE]
> Recomendamos que os editores revejam esta lista de verificação e validem a funcionalidade do módulo antes de submeterem as certificações. Isto irá acelerar o seu processo de certificação reduzindo a necessidade de alterações e resubmissãos.

## <a name="validation-of-image"></a>Validação da imagem

Assim que a imagem do módulo Edge estiver pronta para ser submetida, execute estes passos para garantir que a imagem funciona da forma que a Microsoft espera.

### <a name="steps-to-perform-in-the-azure-portal"></a>Passos a executar no portal Azure

1. Abra o [portal do Azure](https://partner.microsoft.com/).
1. Criar um Grupo de Recursos.
1. Criar um Hub IoT.
1. Crie um dispositivo de borda IoT.
1. Copie a cadeia de ligação e guarde-a no Bloco de Notas.
1. Selecione os **módulos de definição no dispositivo de borda criados**.
1. Adicione os detalhes do ACR onde reside a versão mais recente da imagem.
1. **Selecione adicionar o módulo de borda IoT** e fornecer:
    - A imagem URI na Definição do Módulo
    - A variável ambiental (a mesma que é adicionada no Partner Center)
    - O recipiente cria opções (as mesmas que são adicionadas no Partner Center)
    - A configuração twin módulo (a mesma que é adicionada no Partner Center)
1. Adicione rotas (as mesmas que são adicionadas no Partner Center).
1. Selecione **Rever + Criar**.

Os módulos edge são implantados no dispositivo Edge criado no Azure.

### <a name="steps-to-perform-on-the-device"></a>Passos a executar no dispositivo

#### <a name="device-details"></a>Detalhes do dispositivo

A equipa de certificação utiliza o seguinte hardware para validar imagens em diferentes arquiteturas:

- Para imagens X64, um VM Azure com tamanho de configuração como Standard D2s v3 executa ubuntu Server 18.04/ Ubuntu Server 16.04.
- Para O Gestor de Recursos Azure 32 imagens, um Raspberry Pi 3 Model B.
- Para o Azure Resource Manager 64 imagens, um NVIDIA Jetson Nano 4Gb.

#### <a name="steps"></a>Passos

1. Certifique-se de que os dispositivos/VM criados podem ser acedidos através do Putty.
1. Descarregue [o tempo de execução IoT Edge](../iot-edge/how-to-install-iot-edge.md) para o dispositivo.
1. Atualize a cadeia de ligação copiada no passo 5 para o ficheiro config.yaml.
1. Reinicie o Módulo borda com `sudo systemctl restart iotedge` .
1. Verifique se o módulo está implantado no dispositivo `sudo iotedge list` com; deve estar em funcionamento.
1. Certifique-se de que os registos do módulo implantado `sudo iotedge logs “Module Name“ -f` não têm erros. Se houver erros conhecidos, descreva-o nas Notas do Centro de **Parceiros ao revisor** antes de submeter a oferta.

## <a name="metadata-validation"></a>Validação de metadados

Verifique o seguinte:

- A etiqueta mais recente está listada tanto no Partner Center como no Registo de Contentores Azure.
- O requisito mínimo de hardware é adicionado na descrição da oferta.
- O nome de utilizador e a palavra-passe do registo do contentor Azure são atualizados e adicionados no Partner Center.
- Precisão da **propriedade twin** *desejada, se aplicável.*
- Precisão das **variáveis ambientais** *desejadas, se aplicável.*
- Precisão das **opções de criação** *desejadas, se aplicável*.
- A cadeia de ligação de gestão de chumbo está presente.
- Política de privacidade presente
- Termos de utilização presentes
- Adicionar link de dispositivo ioT edge suportado do catálogo de [dispositivos Azure IoT](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>Passos seguintes

- [Implementar módulos do mercado comercial](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [Publique o Módulo Edge no Partner Center](./partner-center-portal/azure-iot-edge-module-creation.md)
- [Implementar módulo de borda IoT](../iot-edge/quickstart-linux.md)