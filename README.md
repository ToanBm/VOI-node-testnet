# VOI-node-testnet

Cài đặt nhanh: 
1. Run the following commands to download some requirements and add the algorand repository, from which you will be getting your node software and its updates
```Bash
sudo apt install -y jq bc gnupg2 curl software-properties-common
```
```Bash
curl -o - https://releases.algorand.com/key.pub | sudo tee /etc/apt/trusted.gpg.d/algorand.asc
```
```Bash
sudo add-apt-repository "deb [arch=amd64] https://releases.algorand.com/deb/ stable main"
```
2.Run this command to install the node
```Bash
sudo apt update && sudo apt install -y algorand && echo OK
```
3. Stop node algorand
```Bash
sudo systemctl stop algorand && sudo systemctl disable algorand && echo OK
```
5. Set up your shell to run goal
```Bash
echo -e "\nexport ALGORAND_DATA=/var/lib/algorand/" >> ~/.bashrc && source ~/.bashrc && echo OK
```
7. Configure your node for voi
```Bash
sudo algocfg set -p DNSBootstrapID -v "<network>.voi.network" -d /var/lib/algorand/ &&\
sudo algocfg set -p EnableCatchupFromArchiveServers -v true -d /var/lib/algorand/ &&\
sudo chown algorand:algorand /var/lib/algorand/config.json &&\
sudo chmod g+w /var/lib/algorand/config.json &&\
echo OK
```
9. Run this command to fetch the genesis file:
```Bash
sudo curl -s -o /var/lib/algorand/genesis.json https://testnet-api.voi.nodly.io/genesis &&\
sudo chown algorand:algorand /var/lib/algorand/genesis.json &&\
echo OK
```
11. Rename the algorand service to voi:
```Bash
sudo cp /lib/systemd/system/algorand.service /etc/systemd/system/voi.service &&\
sudo sed -i 's/Algorand daemon/Voi daemon/g' /etc/systemd/system/voi.service &&\
echo OK
```
13. Start your node VOI
```Bash
sudo systemctl start voi && sudo systemctl enable voi && echo OK
```
15. Status
```Bash
goal node status
```
17. Fast catch up with the network
goal node catchup $(curl -s https://testnet-api.voi.nodly.io/v2/status|jq -r '.["last-catchpoint"]') &&\
echo OK
18. Wait for fast catchup to complete: sau khi không còn thấy catchpoint thì ctrl + c
goal node status -w 1000
19. Enable Telemetry
sudo ALGORAND_DATA=/var/lib/algorand diagcfg telemetry name -n buiminhphat   <<<<YOUR-NAME>
sudo ALGORAND_DATA=/var/lib/algorand diagcfg telemetry enable &&\
sudo systemctl restart voi
20. Create a node wallet container
goal wallet new voi
21. To create a new account
goal account new
22. display your new account’s mnemonic, use this command:
echo -ne "\nEnter your voi address: " && read addr &&\
goal account export -a $addr
23. Generate your participation keys : nhập 8000000
getaddress() {
  if [ "$addr" == "" ]; then echo -ne "\nNote: Completing this will remember your address until you log out. "; else echo -ne "\nNote: Using previously entered address. "; fi; echo -e "To forget the address, press Ctrl+C and enter the command:\n\tunset addr\n";
  count=0; while ! (echo "$addr" | grep -E "^[A-Z2-7]{58}$" > /dev/null); do
    if [ $count -gt 0 ]; then echo "Invalid address, please try again."; fi
    echo -ne "\nEnter your voi address: "; read addr;
    addr=$(echo "$addr" | sed 's/ *$//g'); count=$((count+1));
  done; echo "Using address: $addr"
}
getaddress &&\
echo -ne "\nEnter duration in rounds [press ENTER to accept default)]: " && read duration &&\
start=$(goal node status | grep "Last committed block:" | cut -d\  -f4) &&\
duration=${duration:-2000000} &&\
end=$((start + duration)) &&\
dilution=$(echo "sqrt($end - $start)" | bc) &&\
goal account addpartkey -a $addr --roundFirstValid $start --roundLastValid $end --keyDilution $dilution
24. Check your participation status
getaddress() {
  if [ "$addr" == "" ]; then echo -ne "\nNote: Completing this will remember your address until you log out. "; else echo -ne "\nNote: Using previously entered address. "; fi; echo -e "To forget the address, press Ctrl+C and enter the command:\n\tunset addr\n";
  count=0; while ! (echo "$addr" | grep -E "^[A-Z2-7]{58}$" > /dev/null); do
    if [ $count -gt 0 ]; then echo "Invalid address, please try again."; fi
    echo -ne "\nEnter your voi address: "; read addr;
    addr=$(echo "$addr" | sed 's/ *$//g'); count=$((count+1));
  done; echo "Using address: $addr"
}
getaddress &&\
goal account dump -a $addr | jq -r 'if (.onl == 1) then "You are online!" else "You are offline." end'

18.You can register your account as participating: trước khi đăng ký vào discord VOI (mục -node-runner-help để faucet 1 VOI với câu lệnh: /voi-testnet-faucet <địa chỉ ví>
getaddress() {
  if [ "$addr" == "" ]; then echo -ne "\nNote: Completing this will remember your address until you log out. "; else echo -ne "\nNote: Using previously entered address. "; fi; echo -e "To forget the address, press Ctrl+C and enter the command:\n\tunset addr\n";
  count=0; while ! (echo "$addr" | grep -E "^[A-Z2-7]{58}$" > /dev/null); do
    if [ $count -gt 0 ]; then echo "Invalid address, please try again."; fi
    echo -ne "\nEnter your voi address: "; read addr;
    addr=$(echo "$addr" | sed 's/ *$//g'); count=$((count+1));
  done; echo "Using address: $addr"
}
getaddress &&\
goal account changeonlinestatus -a $addr -o=1 &&\
sleep 1 &&\
goal account dump -a $addr | jq -r 'if (.onl == 1) then "You are online!" else "You are offline." end'

19. Sau khi đăng ký hoàn tất thì tiếp tục vào faucet tiếp thêm 54 VOI (tùy mỗi tuần mà faucet số lượng được nhiều hay ít)
20. Kiểm tra 
 a) Explorer : https://voi.observer/explorer/home
 b) Giám sát (sau vài giờ sẽ có thông tin trên portal): 
     1. https://cswenor.github.io/voi-proposer-data/health.html
      2. https://voi-node-info.boeieruurd.com/

sudo apt update
sudo systemctl stop voi
sudo apt install algorand
sudo systemctl start voi
goal version -v
goal node status

sudo systemctl status voi
sudo systemctl restart voi
sudo systemctl stop voi
sudo systemctl start voi

