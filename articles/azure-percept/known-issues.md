---
title: Azure Percept conhecido questões
description: Saiba mais sobre as questões conhecidas de Azure Percept e as suas soluções alternativas
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a3f44f3d0cdf024bca12b0023891f21175f52b47
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026943"
---
# <a name="known-issues"></a>Problemas conhecidos

Se encontrar algum destes problemas, não é necessário abrir um inseto. Se tiver problemas com alguma das soluções alternativas, por favor abra um problema.

|Área|Descrição da Edição|Solução|
|-------|---------|---------|
| Experiência de embarque | Não é possível completar a experiência de embarque a menos que o Wi-Fi do dispositivo esteja configurado (o login do Azure falha). | 1. SSH ao ponto de acesso do dispositivo (10.1.1.1) <br> 2. Identificar e copiar o endereço IP do dispositivo ethernet <br> 3. Conecte-se à experiência de embarque utilizando o URL copiado baseado em IP |
| Experiência de embarque | Clicar em links no EULA durante a experiência de embarque às vezes não abre uma nova página web. | Copie o link e abra-o numa janela separada do navegador. |
| Experiência de embarque | Não é possível trabalhar através da experiência de embarque quando ligado a um hotspot de Wi-Fi móvel. | Ligue o seu dispositivo diretamente ao SoftAP, a uma rede Wi-Fi ou a uma rede por ethernet. |
| Wi-Fi/SoftAP | O SoftAP pode por vezes desligar-se ou desaparecer. | Estamos a investigar.  O reinício do dispositivo normalmente traz-o de volta. |
| Wi-Fi | O botão de hardware que alterna o Wi-Fi SoftAP ligado e desligado às vezes não funciona. | Continue a premir o botão ou reinicie o aparelho. |
| Wi-Fi | Os utilizadores podem ver uma mensagem depois de se ligarem a Wi-Fi dizendo "Esta rede Wi-Fi utiliza um padrão de segurança mais antigo." | O hotspot/SoftAP do devkit utiliza o algoritmo de encriptação WEP. |
| Wi-Fi | Não é possível ligar ao SoftAP a partir do Pc do Windows 10 com a seguinte mensagem de erro: <br> "Não se pode ligar a esta rede" | Reinicie o devkit e o computador. |
| Atualização do dispositivo | Os contentores não funcionam após uma atualização OTA. | SSH no dispositivo e reinicie o recipiente IoT Edge com este comando `systemctl restart iotedge` . Isto reiniciará todos os contentores. |
| Atualização do dispositivo | Os utilizadores podem receber uma mensagem de que a atualização falhou, mesmo que tenha sido bem sucedida. | Confirme o dispositivo atualizado navegando para o Device Twin para o dispositivo no Hub IoT. Isto é corrigido após a primeira atualização. |
| Atualização do dispositivo | Os utilizadores podem perder as definições de ligação Wi-Fi após a primeira atualização. | Passe por experiência de embarque após a atualização para configurar a ligação Wi-Fi. Isto é corrigido após a primeira atualização. |
| Atualização do dispositivo | Após a realização de uma atualização OTA, os utilizadores já não podem iniciar sessão via SSH utilizando contas de utilizador previamente criadas, e os novos utilizadores SSH não podem ser criados através da experiência de embarque. Este problema afeta os sistemas que executam atualizações OTA a partir das seguintes versões de imagem pré-instaladas: 2020.110.114.105 e 2020.109.101.105. | Para recuperar os perfis do utilizador, execute estes passos após a atualização da OTA: <br> [SSH no seu devkit](./how-to-ssh-into-percept-dk.md) usando "raiz" como nome de utilizador. Se desativou o login do utilizador "raiz" SSH através de experiência de embarque, deve reativar o login. Executar este comando depois de ligar com sucesso: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Para recuperar os dados anteriores do utilizador, execute o seguinte comando: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Atualização do dispositivo | Depois de tomar uma atualização OTA, os grupos de atualização perdem-se. | Atualize a etiqueta do dispositivo seguindo [estas instruções](./how-to-update-over-the-air.md#create-a-device-update-group). |
| Instalador de pacote de ferramentas Dev | A instalação opcional do Caffe pode falhar se o Docker não estiver a funcionar corretamente no sistema. | Certifique-se de que o Docker está instalado e em funcionamento e, em seguida, re-tentar a instalação do Caffe. |
| Instalador de pacote de ferramentas Dev | A instalação opcional da CUDA falha em sistemas incompatíveis. | Verifique a compatibilidade do sistema com a CUDA antes de executar o instalador. |
| Docker, Network, IoT Edge | Se a sua rede interna utilizar 172.x.x.x, os recipientes de estivadores não conseguirão ligar-se à borda. | Adicione uma secção especial de bip à /etc/docker/daemon.jsem ficheiro como este: `{    "bip": "192.168.168.1/24"}` |
|Estúdio Azure Percept | As ligações "Ver stream" dentro do Azure Percept Studio não abrem uma nova janela mostrando o fluxo web do dispositivo. | 1. Abra o [portal Azure](https://portal.azure.com) e selecione **O Hub IoT**. <br> 2. Clique no Hub IoT ao qual o seu dispositivo está ligado. <br> 3. Selecione **IoT Edge** sob **gestão automática de dispositivos** na sua página IoT Hub. <br> 4. Selecione o seu dispositivo da lista. <br> 5. Selecione **os módulos de definição** na parte superior da página do dispositivo. <br> 6. Clique no ícone do caixote do lixo ao lado **do HostIpModule** para eliminar o módulo. <br> 7. Para confirmar a ação, clique em **Rever + criar** e, em seguida, **Criar**. <br> 8. Abra [o Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) e clique em **Dispositivos** no painel do menu esquerdo. <br> 9. Selecione o seu dispositivo da lista. <br> 10. No **separador Visão,** clique em **Ver o fluxo do seu dispositivo**. O seu dispositivo irá descarregar uma nova versão do HostIpModule e abrir um separador de navegador com o stream web do seu dispositivo. |