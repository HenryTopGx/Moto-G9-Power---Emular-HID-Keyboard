# Moto-G9-Power---Emular-HID-Keyboard
<h3>Tutorial -> Como emular um teclado hid usando o celular Moto G9 Power.</h3>
<hr>
<h3>Requisitos:</h3>
<ul>
  <li>É necessário que o celular Moto G9 power esteja com root usando o Magisk: <a href="https://www.youtube.com/results?search_query=root+magisk+moto+g9+power">youtube</a>;</li>
  <li>Também é necessário estar com o modulo overlayfs instalado e habilitado no Magisk permitindo Read-Write em partições do sistema: <a href="https://github.com/HuskyDG/magic_overlayfs/releases">overlayfs</a>;</li>
  <li>Outro requisito é ter o Termux instalado no celular com python3, você pode encontrá-lo na playstore: <a href="https://play.google.com/store/apps/details?id=com.termux">termux</a></li>
  <li>Por fim um cabo de carregador que conecte seu pc ou o dispositivo que você quer controlar ao seu celular que ira emular um teclado.</li>
</ul>

<h3>1. Comandos(Necessário Termux):</h3>
<p>-- Utilize <code>tsu</code> antes dos comandos a seguir no termux para adquirir root:</p>


* Montar o HID Gadget

<pre><code>mount -t configfs none /sys/kernel/config</code></pre>

* Parâmetros do Dispositivo

<pre><code>cd /sys/kernel/config/usb_gadget/g1
echo 0x1d6b > idVendor
echo 0x0104 > idProduct
echo 0x0100 > bcdDevice
echo 0x0200 > bcdUSB

echo "0123456789" > strings/0x409/serialnumber # Identificador Serial Number do Produto
echo "Manufacturer" > strings/0x409/manufacturer # Nome do Produtor do Produto
echo "HID Keyboard" > strings/0x409/product # Nome do Produto

echo "Config 1" > configs/b.1/strings/0x409/configuration
echo 120 > configs/b.1/MaxPower</code></pre>

* Configuração HID Function

<pre><code>mkdir -p functions/hid.usb0

echo 1 > functions/hid.usb0/protocol
echo 1 > functions/hid.usb0/subclass
echo 8 > functions/hid.usb0/report_length</code></pre>

* Descriptor de Relatório HDI

<pre><code>echo -ne "\x05\x01\x09\x06\xa1\x01\x05\x07\x19\xe0\x29\xe7\x15\x00\x25\x01\x75\x01\x95\x08\x81\x02\x95\x01\x75\x08\x81\x03\x95\x05\x75\x01\x05\x08\x19\x01\x29\x05\x91\x02\x95\x01\x75\x03\x91\x03\x95\x06\x75\x08\x15\x00\x26\xff\x00\x05\x07\x19\x00\x2a\xff\x00\x81\x00\xc0" > functions/hid.usb0/report_desc</code></pre>

* Configurar e Conectar o Gadget
  
<pre><code>ln -s functions/hid.usb0 configs/b.1/

UDC_DRIVER=$(ls /sys/class/udc | head -n 1)
echo $UDC_DRIVER > UDC</code></pre>
-- Se der erro [device or resource busy] nesse último comando você executa o comando a seguir e tenta novamente o comando anterior:
<pre><code>echo > UDC</code></pre>

<h4>Pronto!, caso tudo tenha ocorrido sem erros seu dispositivo Moto G9 Power já é reconhecido como um teclado e você já pode emular teclas usando python, cpp, ou outra linguagem que prefirir usando o termux para compilar e executar...</h4>
