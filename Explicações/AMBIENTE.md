
<div align="center"> <a href="https://git.io/typing-svg"> <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=22&pause=1000000000&color=FFBFEF&center=true&vCenter=true&random=false&width=524&lines=%E2%8A%B9+Executando+o+projeto+no+Conda+%E2%8A%B9+" alt="Typing SVG"> </a> </div><img align="center" alt="" src="./src/header-gif.gif">

Para garantir que o projeto rode corretamente e sem conflitos de versões, o ideal é instalar as dependências a partir do arquivo requirements.txt. 

# <h3 align="center" style="color:#FFBFEF;">1. CRIAR O AMBIENTE VIRTUAL </h3> 

Abra o terminal (ou o prompt do Anaconda) e execute o comando: 

-      conda create -n reconhecimento_yolo python=3.10 
  

Isso cria um novo ambiente chamado reconhecimento_yolo com a versão 3.10 do Python que é compatível com as bibliotecas do projeto. 

 

 
# <h3 align="center" style="color:#FFBFEF;"> 2. ATIVAR O AMBIENTE </h3> 


Depois de criado, ative o ambiente: 

-      conda activate reconhecimento_yolo 
  

 
# <h3 align="center" style="color:#FFBFEF;">  3. INSTALAR AS DEPENDENCIAS </h3> 

Baixe (ou clone) este repositório e, dentro da pasta do projeto, execute: 

-      pip install -r requirements.txt 
  

Esse comando faz o pip instalar automaticamente todas as bibliotecas listadas no arquivo requirements.txt, nas versões exatas utilizadas no meu ambiente Conda e garantindo total compatibilidade. 


 

 
# <h3 align="center" style="color:#FFBFEF;">   4. VERIFICANDO AS INSTALAÇÕES </h3> 

Após a instalação, você pode verificar se tudo está funcionando executando: 

-      python -m paddleocr --help 
  

ou 

-      python -c "import torch; import ultralytics; import paddleocr; print('Tudo funcionando!')" 
  

Se não houver erros, o ambiente está pronto! 

 

 
# <h3 align="center" style="color:#FFBFEF;"> Observações importantes  </h3> 


As versões das bibliotecas estão especificadas no requirements.txt extraído diretamente do meu ambiente Conda. 

Algumas dependências exigem CUDA compatível com a GPU (por exemplo, torch, torchvision, paddlepaddle-gpu). Se estiver usando o Google Colab, basta ativar o modo GPU que ele cuida disso automaticamente. 

Caso esteja rodando localmente, verifique se sua GPU é compatível com CUDA 12.1 (versão usada neste projeto).  
