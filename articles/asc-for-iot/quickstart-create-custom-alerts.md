---
title: Criar alertas personalizados para o Centro de segurança do Azure para pré-visualização do IoT | Documentos da Microsoft
description: Crie e atribua os alertas de dispositivo personalizado para o Centro de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: d793b105e6d73c98739cd05d6e19a218413d7813
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862000"
---
# <a name="quickstart-create-custom-alerts"></a>Início rápido: Criar alertas personalizados

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Utilizar alertas e grupos de segurança personalizada, aproveite totalmente as informações de segurança de ponta a ponta e o conhecimento de dispositivo categóricos para garantir uma maior segurança em sua solução de IoT. 

## <a name="why-use-custom-alerts"></a>Por que usar alertas personalizados? 

Sabe, os dispositivos de IoT melhor.

Para os clientes que compreender totalmente o seu comportamento esperado do dispositivo, Centro de segurança do Azure (ASC) para IoT permite-lhe traduzir os conhecimentos adquiridos para uma política de comportamento do dispositivo e alertar relativamente a qualquer desvio de espera, comportamento normal.

## <a name="security-groups"></a>Grupos de Segurança

Grupos de segurança permitem que defina grupos lógicos de dispositivos e gerenciam o seu estado de segurança de forma centralizada.

Estes grupos podem representar a dispositivos com o hardware específico, dispositivos implementados numa determinada localização ou qualquer outro grupo adequado para suas necessidades específicas.

Grupos de segurança são definidos por uma propriedade tag do segurança módulo duplo com o nome **SecurityGroup**. Altere o valor desta propriedade para alterar o grupo de segurança de um dispositivo.  

Por predefinição, cada solução de IoT no IoT Hub tem um grupo de segurança com o nome **predefinição**.

Utilize grupos de segurança para agrupar os seus dispositivos em categorias lógicas. Depois de criar os grupos, atribuí-las para os alertas personalizados à sua escolha, para a solução ponto a ponto mais eficaz. 

## <a name="customize-an-alert"></a>Personalizar um alerta

1. Abra o seu Hub IoT. 
2. Selecione **Security**, em seguida, selecione **alertas personalizados**. 
3. Escolha os grupos de segurança que pretende aplicar a personalização para. 
4. Clique em **adicionar um alerta personalizado**
5. Introduza um nome de alerta (Observe que os nomes de alerta não podem ser alterados após a criação). 
6. Selecione um comportamento personalizado do alerta na lista pendente. 
7. Editar as propriedades necessárias, clique em **OK**.
8. Certifique-se de clicar **guardar**. Sem a guardar o novo alerta, o alerta é eliminado da próxima vez que fecha o IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Alertas disponíveis para personalização

A tabela seguinte fornece um resumo dos alertas disponíveis para personalização.

| Gravidade | Name                                                                                                    | Origem de Dados | Descrição                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Baixa      | Alerta personalizada – número da cloud para mensagens de dispositivo no protocolo AMQP não se encontra no intervalo permitido          | IoT Hub     | A quantidade de cloud para mensagens do dispositivo (protocolo AMQP) numa janela de tempo não se encontra no intervalo permitido configurada                                  |
| Baixa      | Alerta personalizados - número de cloud rejeitada para mensagens de dispositivo no protocolo AMQP não se encontra no intervalo permitido | IoT Hub     | A quantidade de cloud para mensagens de dispositivo (protocolo AMQP) que foram rejeitados pelo dispositivo numa janela de tempo não se encontra no intervalo permitido configurada |
| Baixa      | Alerta personalizados - número de dispositivo para cloud mensagens no protocolo AMQP não se encontra no intervalo permitido          | IoT Hub     | A quantidade de dispositivo para mensagens de cloud (protocolo AMQP) numa janela de tempo não se encontra no intervalo permitido configurada                                  |
| Baixa      | Invoca de alerta personalizados - número de método direto não está no intervalo permitido                              | IoT Hub     | A quantidade de método direto invoca num tempo de janela não se encontra no intervalo permitido configurada                                                     |
| Baixa      | Alerta personalizados - número de carregamentos de ficheiros não se encontra no intervalo permitido                                       | IoT Hub     | A quantidade de carregamentos de ficheiros numa janela de tempo não se encontra no intervalo permitido configurada                                                              |
| Baixa      | Alerta personalizada – número da cloud para mensagens de dispositivo no protocolo HTTP não está no intervalo permitido          | IoT Hub     | A quantidade de cloud para mensagens do dispositivo (protocolo HTTP) numa janela de tempo não se encontra no intervalo permitido configurada                                  |
| Baixa      | Alerta personalizados - número de cloud rejeitada para mensagens de dispositivo no protocolo HTTP não está no intervalo permitido | IoT Hub     | A quantidade de cloud para mensagens de dispositivo (protocolo HTTP) que foram rejeitados pelo dispositivo numa janela de tempo não se encontra no intervalo permitido configurada |
| Baixa      | Alerta personalizados - número de dispositivo para cloud mensagens no protocolo HTTP não está no intervalo permitido          | IoT Hub     | A quantidade de dispositivo para mensagens de cloud (protocolo HTTP) numa janela de tempo não se encontra no intervalo permitido configurada                                  |
| Baixa      | Alerta personalizada – número da cloud para mensagens de dispositivo no protocolo MQTT não se encontra no intervalo permitido          | IoT Hub     | A quantidade de cloud para mensagens do dispositivo (protocolo MQTT) numa janela de tempo não se encontra no intervalo permitido configurada                                  |
| Baixa      | Alerta personalizados - número de cloud rejeitada para mensagens de dispositivo no protocolo MQTT não se encontra no intervalo permitido | IoT Hub     | A quantidade de cloud para mensagens de dispositivo (protocolo MQTT) que foram rejeitados pelo dispositivo numa janela de tempo não se encontra no intervalo permitido configurada |
| Baixa      | Alerta personalizados - número de dispositivo para cloud mensagens no protocolo MQTT não se encontra no intervalo permitido          | IoT Hub     | A quantidade de dispositivo para mensagens de cloud (protocolo MQTT) numa janela de tempo não se encontra no intervalo permitido configurada                                  |
| Baixa      | Alerta personalizados - número de limpezas de fila de comando não se encontra no intervalo permitido                               | IoT Hub     | A quantidade de fila de comando remove num tempo de janela não se encontra no intervalo permitido configurada                                                      |
| Baixa      | Alerta personalizados - número de atualizações de duplo não se encontra no intervalo permitido                                       | IoT Hub     | A quantidade de atualizações de duplo numa janela de tempo não se encontra no intervalo permitido configurada                                                              |
| Baixa      | Alerta personalizados - número de operações não autorizadas não se encontra no intervalo permitido                            | IoT Hub     | A quantidade de operações não autorizadas numa janela de tempo não se encontra no intervalo permitido configurada                                                   |
| Baixa      | Alerta personalizados - número de ligações ativas não está no intervalo permitido                                        | Agente       | A quantidade de ligações ativas numa janela de tempo não se encontra no intervalo permitido configurada                                                        |
| Baixa      | Foi criada o alerta personalizados - ligação de saída para um ip que não é permitido                              | Agente       | Foi criada uma ligação de saída para um ip que não é permitida                                                                                  |
| Baixa      | Alerta personalizado - número de inícios de sessão falhados locais não se encontra no intervalo permitido                                | Agente       | A quantidade de inícios de sessão locais falhados numa janela de tempo não se encontra no intervalo permitido configurada                                                       |
| Baixa      | Alerta personalizados - início de sessão de um utilizador que não é permitido                                                      | Agente       | Um utilizador local que não é permitido com sessão iniciado dispositivo                                                                                        |
| Baixa      | Alerta personalizados - a execução de um processo que não é permitida                                               | Agente       | Um processo que não é permitido foi executado no dispositivo |          |

## <a name="next-steps"></a>Passos Seguintes

Avance para o artigo seguinte para saber como implementar um agente de segurança...

> [!div class="nextstepaction"]
> [Implementar um agente de segurança](how-to-deploy-agent.md)
