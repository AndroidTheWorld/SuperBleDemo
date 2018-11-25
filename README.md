# SuperBleDemo

[ ![Download](https://api.bintray.com/packages/shiqizhenyes/androidOpenSourceLibrary/SuperBleLib/images/download.svg) ](https://bintray.com/shiqizhenyes/androidOpenSourceLibrary/SuperBleLib/_latestVersion)
![avatar](/image/logo.png)

### 一个很容易的使用的低功耗的蓝牙库

## 初始化

```java
    try {
                SuperBle.getInstance()
                        .setAutoConnect(false)
                        .setContext(this);
            } catch (SuperBleException e) {
                e.printStackTrace();
            }
```

## 开启蓝牙

```java
    superBle.openBluetoothAdapter(new SuperBleOpenCallBack() {
                        @Override
                        public void onSuccess() {
                            Log.d(tag,"开启成功");
                        }

                        @Override
                        public void onFail() {
                            Log.d(tag,"开启失败");
                        }

                        @Override
                        public void onError() {
                            Log.d(tag,"开启错误");
                        }
                    });
```

## 扫描

```java
    superBle.startScan(new SuperBleScanCallBack() {

                        @Override
                        public void onScanStarted(boolean success) {
                            Log.d(tag,"开始扫描"+success);
                        }

                        @Override
                        public void onScanning(BluetoothDevice bluetoothDevice) {

                            if (bluetoothDevice != null) {
                                deviceList.add(bluetoothDevice);
                                initRecycleView();
                            }

                            Log.d(tag,"扫描中："+ bluetoothDevice.getAddress());
                        }

                        @Override
                        public void onScanFinished(List<BluetoothDevice> devices) {
                            Log.d(tag,"扫描完成"+devices.size());

                        }

                        @Override
                        public void onScanFail(SuperBleScanException e) {
                            Log.d(tag,"扫描失败");

                        }

                    });

```

## 连接

```java

    superBle.connect(deviceList.get(position), new SuperBleGattCallBack() {
                        @Override
                        public void onStartConnect(BluetoothDevice device) {
                            Log.d(tag,"开始连接" );
                            mConnectStatus.setText("开始连接");
                        }

                        @Override
                        public void onConnecting(BluetoothDevice device) {
                            Log.d(tag,"连接中" );
                            mConnectStatus.setText(R.string.connecting);
                        }

                        @Override
                        public void onConnectFail(BluetoothDevice device, SuperBleConnectException exception) {
                            Log.d(tag,"连接失败" );
                            mConnectStatus.setText("连接失败");
                        }

                        @Override
                        public void onConnectSuccess(BluetoothDevice device, BluetoothGatt gatt) {
                            Log.d(tag,"连接成功" );
                            startService(gatt,this);
                            mConnectStatus.setText("连接成功");
                        }

                        @Override
                        public void onDisConnecting(boolean isActiveDisConnected, BluetoothDevice device, BluetoothGatt gatt) {
                            Log.d(tag,"正在断开" );
                            mConnectStatus.setText("正在断开");
                        }

                        @Override
                        public void onDisConnected(boolean isActiveDisConnected, BluetoothDevice device, BluetoothGatt gatt) {
                            Log.d(tag,"已断开" );
                            mConnectStatus.setText("已断开");

                        }
                    });

```

## 开始服务

```java

     superBle.initService(gatt, serverUUID, SuperBleGatt, new SuperBleServerCallBack() {

                @Override
                public void onServerSuccess(BluetoothGatt gatt, BluetoothGattService bluetoothGattService, SuperBleGatt superBleGatt) {
                    startNotification(gatt, superBleGatt);
                }

                @Override
                public void onServerFail(BluetoothGatt gatt, mobi.zack.superblelib.inter.SuperBleGatt superBleGatt) {

                }
            });
```

## 订阅通知

```java

    superBle.initNotification(gatt, notifyUUID, superBleGatt, new SuperBleNotifyCallBack() {

                @Override
                public void onNotifySuccess(BluetoothGatt gatt, BluetoothGattCharacteristic notifyCharacteristic, SuperBleGatt superBleGatt) {
                    Log.d(tag,"订阅成功");
                    startDescribe(gatt, superBleGatt, notifyCharacteristic);
                }

                @Override
                public void onNotifyFail(BluetoothGatt gatt, SuperBleGatt superBleGatt) {
                    Log.d(tag,"订阅失败");
                }
            });
```

## 特征描述

```java
    superBle.initDescriptor(gatt, superBleGatt , characteristic, new SuperBleDescribeCallBack() {
                @Override
                public void onSuccess(BluetoothGatt gatt, SuperBleGatt superBleGatt, BluetoothGattDescriptor descriptor) {
                    setSuperBleGatt(superBleGatt);
                    Log.d(tag,"写入descriptor成功");

                }

                @Override
                public void onFail(BluetoothGatt gatt, SuperBleGatt superBleGatt) {
                    Log.d(tag,"写入descriptor失败");
                }
            });
```

## 写入数据

```java
    superBle.write(writeUUID, data.getBytes(), superBleGatt, new SuperBleWriteCallBack() {

                            @Override
                            public void onSuccess(BluetoothGattCharacteristic characteristic) {

                            }

                            @Override
                            public void onFail(BluetoothGattCharacteristic characteristic) {

                            }

                            @Override
                            public void onCharacteristicChanged(BluetoothGatt gatt, BluetoothGattCharacteristic characteristic) {
                                byte[] data = characteristic.getValue();
                                try {
                                    String s = new String(data, "utf-8");
                                    Log.d(tag, "设诶返回数据："+ s);
                                } catch (UnsupportedEncodingException e) {
                                    e.printStackTrace();
                                }
                            }
                        });
```
