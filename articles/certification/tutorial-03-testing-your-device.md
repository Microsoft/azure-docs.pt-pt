---
title: Programa Azure Certified Device - Tutorial - Testar o seu dispositivo
description: Um guia passo a passo para testar o seu dispositivo com o serviço AICS no portal Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310959"
---
# <a name="tutorial-test-and-submit-your-device"></a>Tutorial: Teste e envie o seu dispositivo

A próxima fase principal do processo de certificação (embora possa ser concluída antes de adicionar os detalhes do dispositivo) envolve testar o seu dispositivo. Através do portal, utilizará o Serviço de Certificação Azure IoT (AICS) para demonstrar o desempenho do seu dispositivo de acordo com os nossos requisitos de certificação. Depois de passar com sucesso na fase de testes, submeterá o seu dispositivo para revisão final e aprovação pela equipa de Certificação Azure!

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligue o seu dispositivo ao IoT Hub utilizando o Serviço de Provisionamento de Dispositivos (DPS)
> * Teste o seu dispositivo de acordo com o seu(s) programa de certificação selecionado)
> * Envie o seu dispositivo para revisão pela equipa de Certificação Azure

## <a name="prerequisites"></a>Pré-requisitos

- Deverá fazer o seu contrato e ter um projeto para o seu dispositivo criado no [portal Azure Certified Device](https://certify.azure.com). Para mais informações, consulte o [tutorial.](tutorial-01-creating-your-project.md)
- (Opcional) Aconselhamos que prepare o seu dispositivo e verifique manualmente o seu desempenho de acordo com os requisitos de certificação. Isto porque se pretender voltar a testar com códigos de dispositivos diferentes ou programa de certificação, terá de criar um novo projeto.

## <a name="connecting-your-device-using-dps"></a>Ligar o seu dispositivo utilizando o DPS

Todos os dispositivos certificados são necessários para demonstrar a capacidade de se ligar ao IoT Hub utilizando DPS. Os passos seguintes acompanham-no como ligar com sucesso o seu dispositivo para testes no portal.

1. Para iniciar a fase de testes, selecione o `Connect & test` link na página de resumo do projeto:  

    ![Ligação e ligação de teste](./media/images/connect-and-test-link.png)

1. Dependendo da certificação selecionada, verá os testes necessários na página 'Connect & test'. Reveja estes para garantir que está a candidatar-se ao programa de certificação correto.  

    ![Ligar e testar a página](./media/images/connect-and-test.png)

1. Ligue o seu dispositivo ao IoT Hub utilizando o Serviço de Provisionamento de Dispositivos (DPS). O DPS suporta opções de conectividade de chaves simétricas, certificação X.509 e um Módulo de Plataforma Fidedigna (TPM). Isto é necessário para todas as certificações.

    - *Para obter mais informações sobre a ligação do seu dispositivo ao Azure IoT Hub com dPS, visite [a visão geral dos dispositivos de provisionamento](../iot-dps/about-iot-dps.md "Visão geral do serviço de fornecimento de dispositivos").*
    
1. Se utilizar teclas simétricas, pedir-lhe-á-á que configuure o DPS com o âmbito DPS ID fornecido, iD do dispositivo, chave de autenticação e ponto final DPS. Caso contrário, ser-lhe-á pedido que forneça certificado X.509 ou chave de endosso.

1. Depois de configurar o seu dispositivo com DPS, confirme a ligação clicando `Connect` no botão na parte inferior da página. Após uma ligação bem sucedida, pode proceder à fase de teste clicando no `Next` botão.  

    ![Ligar e testar conectados](./media/images/connected.png)

## <a name="testing-your-device"></a>Testar o seu dispositivo

Depois de ter ligado com sucesso o seu dispositivo ao AICS, está agora pronto para executar os testes de certificação específicos do programa de certificação para o qual se está a candidatar.

1. **Para a certificação do Dispositivo Certificado Azure**: No separador 'Selecione a capacidade do dispositivo', irá rever e selecionar quais os testes que pretende executar no seu dispositivo.
1. **Para a certificação IoT Plug and Play**: Reveja cuidadosamente os parâmetros que serão verificados durante o teste que declarou no modelo do seu dispositivo.
1. **Para a certificação Edge Managed**: Não são necessários passos adicionais para além de demonstrar conectividade.
1. Uma vez concluídos os preparativos necessários para o programa de certificação especificado, selecione `Next` para proceder à fase de 'Teste'.
1. Selecione `Run tests` na página para começar a executar AICS com o seu dispositivo.
1. Assim que tiver recebido uma notificação de que passou nos testes, selecione `Finish` para voltar à sua página de resumo.

![Teste passado](./media/images/test-pass.png)

7. Se tiver questões adicionais ou precisar de assistência para resolver problemas com a AICS, visite o nosso guia de resolução de problemas.

> [!NOTE]
> Embora possa concluir o processo de certificação on-line para IoT Plug e Play e Edge Managed sem ter de submeter o seu dispositivo para revisão manual, poderá ser contactado por um membro da equipa do Azure Certified Device para posterior validação do dispositivo para além do testado através do nosso serviço de automação.

## <a name="submitting-your-device-for-review"></a>Submeter o seu dispositivo para revisão

Uma vez concluídas todas as áreas obrigatórias na secção "Detalhes do Dispositivo" e passando com sucesso nos testes automatizados no processo "Connect & test", pode agora notificar a equipa de Dispositivos Certificados Azure de que está pronto para revisão de certificação.

1. selecione `Submit for review` na página de resumo do projeto:  

    ![Rever e certificar link](./media/images/review-and-certify.png)

1. Confirme a sua submissão na janela pop-up. Uma vez submetido um dispositivo, todos os detalhes do dispositivo serão lidos apenas até que seja solicitada a edição. (Ver [como editar as informações do seu dispositivo após a publicação](./how-to-edit-published-device.md).)  

    ![Iniciar o diálogo de revisão da certificação](./media/images/start-certification-review.png)

1. Uma vez apresentado o projeto, a página de resumo do projeto indicará que o projeto é `Under Certification Review` da equipa de Certificação Azure:  

    ![Sob revisão](./media/images/review-and-certify-under-review.png)

1. Dentro de 5-7 dias úteis, espere uma resposta por e-mail da equipa de Certificação Azure para o endereço fornecido no seu perfil da empresa sobre o estado da submissão do seu dispositivo.

    - Submissão aprovada  
        Uma vez que o seu projeto tenha sido revisto e aprovado, receberá um e-mail. O e-mail incluirá um conjunto de ficheiros, incluindo o crachá do Dispositivo Certificado Azure, as diretrizes de utilização do crachá e outras informações sobre como amplificar a mensagem de que o seu dispositivo está certificado. Parabéns!

    - Submissão pendente  
        Caso o seu projeto não seja aprovado, poderá fazer alterações aos detalhes do projeto e, em seguida, reenviar o dispositivo para certificação uma vez pronto. Será enviado um e-mail com informações sobre o motivo pelo qual o projeto não foi aprovado e medidas para reenviar para certificação.

## <a name="next-steps"></a>Passos seguintes

Parabéns! O seu dispositivo já passou com sucesso em todos os testes e foi aprovado através do programa Azure Certified Device. Pode agora publicar o seu dispositivo no nosso catálogo de Dispositivos Certificados Azure, onde os clientes podem fazer compras para os seus produtos com confiança no seu desempenho com a Azure.
> [!div class="nextstepaction"]
> [Tutorial: Publicar o seu dispositivo](tutorial-04-publishing-your-device.md)

