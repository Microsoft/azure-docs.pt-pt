---
title: Arranque rápido para configurar e implementar GPU Azure Stack Edge | Microsoft Docs
description: Começa com a implementação do GPU Azure Stack Edge depois de o dispositivo ser recebido.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to quickly deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f3ecb6c9aa4ec200cd1a53b82d7c81a2c8d4dcd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546793"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Quickstart: Começa com o Azure Stack Edge Pro com GPU 

Este arranque rápido detalha os pré-requisitos e os passos necessários para implementar o seu dispositivo GPU Azure Stack Edge Pro. Os passos de início rápido são efetuados no portal do Azure e na IU da Web local do dispositivo. 

O procedimento total deve demorar aproximadamente 1,5 horas a ser concluído. Para instruções detalhadas passo a passo, vá ao [Tutorial: Prepare-se para implementar o GPU Azure Stack Edge Pro .](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist) 


## <a name="prerequisites"></a>Pré-requisitos

Antes de partir, certifique-se de que estão em vigor os seguintes pré-requisitos:

1. O dispositivo GPU Azure Stack Edge Pro é entregue no seu site, [desembalado](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) e [montado na cremalheira](azure-stack-edge-gpu-deploy-install.md#rack-the-device). 
1. Configure a sua rede de modo a que o seu dispositivo possa alcançar os [padrões e portas de URL listados](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements). 
1. Tem acesso ao proprietário ou colaborador da assinatura Azure.
1. No portal Azure, aceda a **Subscrições de > Domiciliários > os fornecedores de recursos > de subscrição da sua subscrição.** Procure `Microsoft.DataBoxEdge` e registe o fornecedor de recursos. Repita para se registar `Microsoft.Devices` se criar um recurso IoT Hub para implementar cargas de trabalho computacional.
1. Certifique-se de que tem um mínimo de 2 IPs livres, estáticos e contíguos para nós Kubernetes e pelo menos 1 IP estático para o serviço IoT Edge. Para cada módulo ou serviço externo, você implementará, você precisará de 1 IP adicional.
1. Consulte a [lista de verificação de implementação](azure-stack-edge-gpu-deploy-checklist.md) para obter tudo o que necessita para a configuração do dispositivo. 


## <a name="deployment-steps"></a>Passos da implementação

1. **Instalação:** Ligue a PORTA 1 a um computador cliente através de um cabo de cruzamento ou adaptador USB Ethernet. Ligue pelo menos uma outra porta de dispositivo para obter dados, de preferência 25 GbE( da PORTA 3 a PORT 6) para a Internet através de pelo menos 1 comutador de GbE e cabos de cobre SFP+. Ligue os cabos de alimentação fornecidos às Unidades de Alimentação e separe as tomadas de distribuição de energia. Pressione o botão de alimentação no painel frontal para ligar o aparelho.  

    Consulte [a Matriz de Interoperabilidade da Série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e a dupla porta [Mellanox 25G ConnectX-4 produtos compatíveis com a rede](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) de canais para obter cabos e interruptores de rede compatíveis.

    Aqui está a configuração mínima de cablagem necessária para implantar o seu dispositivo:  ![ Plano traseiro de um dispositivo por cabo](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Conecte**: Configurar as definições IPv4 do adaptador Ethernet no seu computador com um endereço IP estático de **192.168.100.5** e sub-rede **255.255.255.0**. Abra o seu navegador e ligue-se à UI web local do dispositivo em https://192.168.100.10 . Esta operação poderá demorar alguns minutos. Continue na página web quando vir o aviso do certificado de segurança.

3. **Iniciar sposição :** Inscreva-se no dispositivo com senha padrão *Password1*. Altere a palavra-passe do administrador do dispositivo. A palavra-passe deve conter entre 8 a 16 caracteres, e 3 dos caracteres maiúsculas, minúsculas, numéricas e especiais.

4. **Rede de configuração**: Aceite a configuração DHCP predefinida para a porta de dados conectada se tiver um servidor DHCP na sua rede. Caso contrário, forneça um IP, servidor DNS e gateway predefinido. Ver mais informações sobre [as definições da Rede](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network).

5. **Configure a rede de cálculo**: Crie um interruptor virtual ativando uma porta no seu dispositivo. Introduza 2 IPs estáticos contíguos para os nós Kubernetes na mesma rede que criou o interruptor. Forneça pelo menos 1 IP estático para o serviço IoT Edge Hub para aceder a módulos de computação e 1 IP estático para cada serviço ou recipiente extra que pretende aceder a partir do cluster Kubernetes. 

    Kubernetes é necessário para implantar todas as cargas de trabalho contentorizadas. Consulte mais informações sobre [as definições da rede Compute](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

6. **Configure o proxy web**: Se utilizar um representante web no seu ambiente, insira o servidor de procuração web IP em `http://<web-proxy-server-FQDN>:<port-id>` . Desata autenticação a **Nenhum**. Consulte mais informações sobre [as definições de procuração web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy).

7. **Dispositivo de configuração**: Introduza um nome de dispositivo e domínio DNS ou aceite predefinições. 

8. **Servidor de Atualização configurar**: Aceite o servidor padrão da Atualização do Microsoft ou especifique um servidor de Serviços de Atualização do Servidor do Windows Server (WSUS) e o caminho para o servidor. 

9. **Configurações de tempo de configuração**: Aceite as definições de tempo predefinidas ou o fuso horário definido, o servidor NTP primário e o servidor NTP secundário na rede local ou como servidores públicos.

10. **Configure os certificados**: Se alterar o nome do dispositivo e/ou o domínio DNS, então deve gerar certificados ou adicionar certificados para ativar o dispositivo. 

    - Para testar cargas de trabalho não produtivos, utilize [a opção De obter certificados](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). 
    - Se trouxer os seus próprios certificados, incluindo a(s) cadeia de assinaturas, [adicione certificados](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) em formato apropriado. Certifique-se de carregar a corrente de assinatura primeiro. Consulte [Criar certificados](azure-stack-edge-gpu-create-certificates-tool.md) e [carregar certificados através da UI local.](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates)

11. **Ativar**: Para obter a chave de ativação 

    1. No portal Azure, aceda ao **recurso Azure Stack Edge > visão geral > configuração do dispositivo > Ativar > Gerar tecla**. Copie a chave. 
    1. Na UI web local, vá a **Get start > Ative** e fornecer a chave de ativação. Quando a chave é aplicada, o dispositivo demora alguns minutos para a ativação. Descarregue o `<device-serial-number>` ficheiro .json quando solicitado para armazenar com segurança as chaves do dispositivo necessárias para uma futura recuperação. 

12. **Configure computação**: No portal Azure, vá ao **Overview > Dispositivo**. Verifique se o dispositivo está **online.** No painel esquerdo, vá ao **Edge compute > Começar > computação Configure Edge > Compute**. Forneça um serviço IoT Hub existente ou novo e aguarde cerca de 20 minutos para que o cálculo se configuure. Ver mais informações sobre [tutorial: Conigure compute no dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md)

Está pronto para implementar cargas de trabalho computacional no seu dispositivo [via IoT Edge,](azure-stack-edge-gpu-deploy-sample-module-marketplace.md) [via `kubectl` ](azure-stack-edge-gpu-create-kubernetes-cluster.md) ou [via Azure Arc ativado kubernetes](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)! Se tiver algum problema durante a configuração, aceda a [problemas de dispositivos de resolução de problemas,]() [questões de encomenda,](azure-stack-edge-gpu-troubleshoot.md) [problemas de certificados](azure-stack-edge-gpu-certificate-troubleshooting.md)ou problemas com Kubernetes. 

## <a name="next-steps"></a>Passos seguintes

[Instalar Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-install.md)



