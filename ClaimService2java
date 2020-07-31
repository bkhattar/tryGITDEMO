/*
 * To change this template, choose Tools | Templates
 * and open the template in the editor.
 */
 
 
package com.xchanging.cas.restful.service;


	public Response service1(@Context HttpHeaders httpHeaders,	WSPublicSummaryRequestDTO wSPublicSummaryRequestDTO) {

		long startTime = System.currentTimeMillis();
		logger.info("Enter - getPublicComments()");
		logger.debug("Input: " + wSPublicSummaryRequestDTO);

		WSPublicCommentsResponseDTO wSPublicCommentsResponseDTO = null;
		CASServiceHelper wSCASServiceHelper = new CASServiceHelper();

		// Populate UserDTO Object
		if (httpHeaders == null) {
			logger.debug("HttpHeaders coming is null.");
			wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(Constants.ERR_1000,
					Constants.ERR_DESC_1000);
		} else {
			try {
				List<String> errors = FinancialSummaryHelper.validateInput(wSPublicSummaryRequestDTO);
				if (errors.size() > 0) {
					wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(Constants.ERR_1001,
							CASServiceHelper.prepareValidationErrorDesc(errors));
				} else {
					UserDTO userDTO = null;
					ClaimDTO lClaimDTO = new ClaimDTO();
					// prepare UserDTO object
					userDTO = wSCASServiceHelper.populateUserDTO(httpHeaders);

					// Prepare ClaimDTO object
					PolicyDTO lPolicyDTO = new PolicyDTO();
					TransactionDTO lTransactionDTO = new TransactionDTO();
					lTransactionDTO.setTransactionReference(wSPublicSummaryRequestDTO.getTransaction());
					Collection<TransactionDTO> transactions = new LinkedList<TransactionDTO>();
					transactions.add(lTransactionDTO);
					lPolicyDTO.setUmr(wSPublicSummaryRequestDTO.getUmr());
					lClaimDTO.setUcr(wSPublicSummaryRequestDTO.getUcr());
					lClaimDTO.setTransactions(transactions);
					lClaimDTO.setBureau(wSPublicSummaryRequestDTO.getBureau());
					lClaimDTO.setPolicyDTO(lPolicyDTO);

					ClaimRequestDTO claimRequestDTO = new ClaimRequestDTO();
					claimRequestDTO.setUcr(wSPublicSummaryRequestDTO.getUcr());
					claimRequestDTO.setUmr(wSPublicSummaryRequestDTO.getUmr());
					TransactionService lTransactionService = (TransactionService) ServiceBeanLocator.getInstance()
							.getServiceBean(Constants.TRANSACTION_SERVICE);
					TransactionDetailsResponseDTO lTransactionDetailsResponseDTO = lTransactionService
							.getTransactionDetails(lClaimDTO, userDTO);
					logger.debug("Role from the Tranaction Details response DTO: "
							+ lTransactionDetailsResponseDTO.getClaimDTO().getRole());

					logger.debug("Got User DTO as Account Code - [" + userDTO.getAccountCode() + "] userId ["
							+ userDTO.getUserId() + "]");

					if (lTransactionDetailsResponseDTO != null && lTransactionDetailsResponseDTO.getClaimDTO() != null
							&& StringUtils.isNotBlank(lTransactionDetailsResponseDTO.getClaimDTO().getRole())) {
						logger.debug("TransactionDetail is not null");
						lClaimDTO.setRole(lTransactionDetailsResponseDTO.getClaimDTO().getRole());
						logger.debug(
								"Got Claim DTO as ucr - [" + lClaimDTO.getUcr() + "] userId [" + lClaimDTO.getRole()
										+ "] Bureau [" + lClaimDTO.getBureau() + "],TR[" + lClaimDTO.getTr() + "]");
						if (lTransactionDetailsResponseDTO.getScreenType() == 7) {
							logger.info("Class down screen is to be shown");
							wSPublicCommentsResponseDTO = PublicCommentsHelper
									.preparePublicCommentsResponse(Constants.ERR_1004, Constants.ERR_DESC_1004);

						} else if (lTransactionDetailsResponseDTO.getScreenType() == 10) {
							// This is for Screen Type of XCS Claims
							wSPublicCommentsResponseDTO = PublicCommentsHelper
									.preparePublicCommentsResponse(Constants.ERR_1004, Constants.ERR_DESC_1004);

						} else {
							wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(
									Constants.SUCCESS_CODE, Constants.EMPTY_RESPONSE_DESC);
							ResponseUpdateDTO responseUpdateDTO = lTransactionService
									.getTransactionResponseDetails(userDTO, lClaimDTO);
							if (null != responseUpdateDTO) {
								wSPublicCommentsResponseDTO
										.setPublicComments(FinancialSummaryHelper.getPublicCommentsAsString(
												responseUpdateDTO.getResponseCommnetsDTO().getPreviousPublicComments(),
												responseUpdateDTO.getResponseCommnetsDTO()
														.getPublicCommentVisibility()));
							} else {
								wSPublicCommentsResponseDTO.setPublicComments("");
							}
						}
					} else {
						logger.info("TransactionDetail is null or claimDetails is null or role is blank.");
						wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(
								Constants.ERR_1003, CASServiceHelper.getGenericErrorMessage());
					}
				}
			} catch (WebHandlerException whex) {
				logger.error("Exception occurred while populating the userData", whex);
				wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(Constants.ERR_1003,
						CASServiceHelper.getGenericErrorMessage());
			} catch (XSHRemoteServiceException ex) {
				logger.error("XSHRemoteServiceException ", ex);
				wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(Constants.ERR_1003,
						CASServiceHelper.getGenericErrorMessage());
			} catch (NamingException ex) {
				logger.error("NamingException ", ex);
				wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(Constants.ERR_1003,
						CASServiceHelper.getGenericErrorMessage());
			} catch (Exception ex) {
				logger.error("Exception ", ex);
				wSPublicCommentsResponseDTO = PublicCommentsHelper.preparePublicCommentsResponse(Constants.ERR_1003,
						CASServiceHelper.getGenericErrorMessage());
			}
		}
		logger.debug("Output of Public Comments : " + wSPublicCommentsResponseDTO);
		long endTime = System.currentTimeMillis();
		logger.info("Exit - getPublicComments() Time taken: " + (endTime - startTime) + " milliseconds");
		return Response.status(Constants.REST_RESPONSE_SUCCESS_CODE).entity(wSPublicCommentsResponseDTO).build();
	}
}
