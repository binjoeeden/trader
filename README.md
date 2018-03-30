�����


1) xcoin_api_client.py�� __init__ �Լ��� api_key �� api_secret ������ ���� api key���� secret ���� �־��ּ���

2) caller �ʿ��� �Ʒ��� ���� import
 - from rest_api import *
 
3) �Լ� ȣ�� ��� 
	- call_api (api_type, currency, rgParam={}, t_sleep=5)
	- �Ķ���͵�
		api_type : api type���� �Ʒ��� ���� enum�� �־��ּ��� (���ڿ� �ƴ�)
			GET_PRC		# Get Price (���� �ü� ��������)
			MKT_BID		# MARKET_BID (���尡 ����)
			MKT_ASK		# MARKET_ASK (���尡 �Ǹ�)
			REQ_ORD		# REQ_ORD	 (������ �ż� �Ǵ� �ŵ�) : rgParam�� ���� ��������, ����, �ż�/�ŵ� Ÿ�� ����
			CCL_ORD		# CANCEL ORDER  �Ÿ� ��� �� ���
			CHK_ORD		# CHECK ORDER (������ �ż�/�ŵ� ����� ���������� �̰��� ������� ������. 
						# �Ʒ��� ������ ������ chk_order �Լ��� ����ϼ���. 
			GET_BAL		# Get Balance (���� ���� ��ȸ)
		currency : 'BTC', 'EOS', ....
		rgParam�� �ʿ��� ���ڸ� map���� ����. �Ʒ� ���ÿ����� �����ؼ� �ʿ��� ���� Ȯ���ϼ���.
		t_sleep : ���� ������ try again �� ������ ���� ���� �ֽ��ϴ�.  call_api�� ���� ������ ������ �ݺ� �õ��ϴ� �� 
				  �ݺ� �õ��ϱ� ���� sleep interval �Դϴ�. (���� : ��). �⺻���� 5�� ���� ��õ� �մϴ�.

	���� ����
	- ���� ���� ������
	>>> from rest_api import *
	>>> r = call_api(GET_PRC, 'EOS')		// EOS ���� ���� ����
	>>> r
	{'status': '0000', 'data': {'opening_price': '6510', 'closing_price': '6560', 'min_price': '6350', 'max_price': '6980', 'average_p
	rice': '6642.4484', 'units_traded': '34792947.15503703', 'volume_1day': '34792947.15503703', 'volume_7day': '203422280.92040176000
	0000000', 'buy_price': '6560', 'sell_price': '6570', 'date': '1522421010457'}}

	���� ���� : curr_eos_prc =  r['data']['closing_price']

	- ������ �ż�/�ŵ� �� rgParam�� 'type' �Ӽ��� 'bid'�� 'ask'�� �������ְ� �������� ����
	- ������ �ż�
	>>> r = call_api(REQ_ORD, 'EOS', {'units':0.1, 'price':7000, 'type':'bid'})		# 7000���� 0.1�� �ż� 
	>>> r
	{'status': '0000', 'order_id': '1522240318275677', 'data': [{'contNo': '15288358', 'units': '0.100000000000000000', 'price': '6760
	', 'total': 676, 'fee': 0.00015}]}

	-> order_id ���� r['order_id']
	-> r['status']=='0000' �̸� �ż� �Ǵ� �ŵ��� ������ ���������� �ɸ� ��.
	
	- ������ �ŵ�
	>>> r = call_api(REQ_ORD, 'EOS', {'units':0.1, 'price':7000, 'type':'ask'})		# 7000���� 0.1�� �ŵ� 
	>>> r
	{'status': '0000', 'order_id': '1522240512184440', 'data': [{'cont_id': '15288637', 'units': '0.100000000000000000', 'price': '678
	0', 'total': 678, 'fee': 1}]}
	

	- �ŷ��� ü��Ǿ����� �Ǵ��ϴ� �Լ� (bid/ask type���� �������־�� ��)
	- chk_order(order_id, currency, bidask, amnt)
	- �Ķ���͵�
		order_id�� ü��Ǿ����� Ȯ���ϰ��� �ϴ� �Ÿ� �� id�� REQ_ORD �� ���� ���ϰ���� �� 'order_id'�Ӽ��� ����� �־��ݴϴ�.
		currency : ���� ���ڿ� 'EOS', 'BTC', ...
		bidask :  bid ���� ask���� enum ���� �־��ּ���. BID, ASK   �� ���� ����� ���ǵǾ� ����. ���ڿ��� �ƴմϴ�.
		amnt   :  �ż��� �ŵ� �ɾ��� ����(amount)�� �־��ּ���. �̸� �������� ü��Ǿ����� �Ǵ��մϴ�. 
		  -> �ŷ��� �ټ��� ������ ü��� �� �ִ� �� ����� ������ �ŷ� �� ������ �ɴϴ�. �� �ŷ��ǵ��� ü�� ������ ��� ���ؼ�
		     �� ������ ���ؼ� ü��Ϸ�Ǿ����� �Ǵ��մϴ�. 

	���� ����
	>>> rr = chk_order('1522240512184440', 'EOS', ASK, 0.1)	# r�� ������ �ŵ� ���� ���
	>>> rr
	{'result': True, 'status': '0000', 'amnt': 0.1, 'krw': 678, 'fee': 1.0, 'prcs': 6780, 'date': 20180328, 'time': 213512}

	rr['result'] == True �̸� �ŷ��� ü��� ���Դϴ�.
	 : amount == rr['amount'] �� �ŷ� ü�Ῡ�θ� �Ǵ�����������. bid�� ��� ������ ������ ���� ������ return �˴ϴ�.
	
	- �ŷ� ��� �Լ�
	- ccl_order(order_id, crcy, bidask)		# cancel order
	r = ccl_order(r['order_id'], 'EOS', ASK)
	
	
