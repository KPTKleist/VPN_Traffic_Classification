# VPN_Traffic_Classification

This project utilizes a convolutional neural network (CNN) to differentiate different types of VPN traffic based on the payload data of network packets.

### Dataset

The VPN portion of data from [VPN-nonVPN dataset (ISCXVPN2016)](https://www.unb.ca/cic/datasets/vpn.html) was used for training the model, specifically `vpn_email2a.pcap`, `vpn_email2b.pcap`, `vpn_facebook_audio2.pcap`, `vpn_facebook_chat1a.pcap`, `vpn_facebook_chat1b.pcap`, `vpn_skype_files1a.pcap` and `vpn_youtube_A.pcap` for data across 5 categories of activities.

>Gerard Drapper Gil, Arash Habibi Lashkari, Mohammad Mamun, Ali A. Ghorbani, "Characterization of Encrypted and VPN Traffic Using Time-Related Features", In Proceedings of the 2nd International Conference on Information Systems Security and Privacy(ICISSP 2016) , pages 407-414, Rome, Italy.

### Data Extraction

To extract the payload data from the packets, [nPrint](https://nprint.github.io/nprint/) is required to be installed either on Debian or MacOS. Prior to data extraction, packets other than TCP packets with payload are discarded using Wireshark.

>Holland, Jordan and Schmitt, Paul and Feamster, Nick and Mittal, Prateek, "nPrint: A Standard Data Representation for Network Traffic Analysis", arXiv:2008.02695

* Output the first 1024 bytes of packet payload as bits from the Pcap file into a CSV file.
```
$ nprint -P filename.pcap -p 1024 -W filename.csv
```

* Generate images from CSV files for training, use `--i` to specify the starting index of images.
```
$ python csv2img.py --r filename.csv --w ./image_path/ --i 0
```

### Training

The training data should be placed under the same directory and follow the structure
```
VPN_Traffic/
...class_a/
......0.png
......1.png
...class_b/
......0.png
......1.png
```

[Preprocessed dataset](https://drive.google.com/drive/folders/1uIf0KGBn1aBV3BhQOrMi3DAdQHFzFKzc?usp=sharing) can be directly used for training after being extracted. Alternatively, follow the guide above to generate other datasets for training.

The model achieved an accuracy of 90% after 20 epochs.

### Inference

* Try the model on an image generated from a packet. Use `--m` to input the saved model checkpoint directory, which is `./callbacks_alexnet/model.h5` by default.
```
$ python inference.py --m ./callbacks_alexnet/model.h5 --r filename.png
```

* Or, use the following command to run inference on all data from one category.
```
$ python inference_all.py --m ./callbacks_alexnet/model.h5 --r ./VPN_Traffic/classname/ --n number_of_files --l classname
```
