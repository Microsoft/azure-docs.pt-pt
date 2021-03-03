---
title: Lista de verificação de pré-certificação para ofertas de módulo IoT Edge no Azure Marketplace
description: Conheça os requisitos específicos de certificação para a publicação de módulos IoT Edge no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 4782e6c0ed866c467051fc1811a60df0010a3db1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746272"
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
1. Descarregue [o tempo de execução IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge) para o dispositivo.
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

## <a name="next-steps"></a>Passos seguintes

- [Implementar módulos do mercado comercial](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal#deploy-from-azure-marketplace)
- [Publique o Módulo Edge no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)
- [Implementar módulo de borda IoT](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  
