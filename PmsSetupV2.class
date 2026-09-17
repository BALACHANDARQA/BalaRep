//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpRequest.BodyPublishers;
import java.net.http.HttpResponse.BodyHandlers;
import java.util.ArrayList;
import java.util.List;

public class PmsSetupV2 {
    static ObjectMapper mapper = new ObjectMapper();
    static HttpClient client = HttpClient.newHttpClient();
    static String tenantId = "127781";
    static String startDate = "2026-03-17";
    static String propertyId = "18999";
    static String token = "fb0e3441-de67-401d-875a-a349e53ffc90";
    static String rateCategoryId = "aee20492-8f6d-43b4-a347-d5148bfdc2c1";
    static String propertyBaseUrl = "https://aks-stay-mi-perf-03-eastus.hospitalityrevolution.com/property-service";
    static String paymentBaseUrl = "https://aks-stay-mi-perf-03-eastus.hospitalityrevolution.com/payment-service";
    static String accountBaseUrl = "https://aks-stay-mi-perf-03-eastus.hospitalityrevolution.com/account-service";
    static String rateBaseUrl = "https://aks-stay-mi-perf-03-eastus.hospitalityrevolution.com/rate-service";
    static final String CATEGORY_NAME = "Game";
    static final String SUB_CATEGORY_NAME = "Casino";
    static final String POLICY_NAME = "Casino_CXL_KB";
    static final String RUN_SUFFIX = String.valueOf(System.currentTimeMillis() % 10000L);
    static final List<String> ITEM_NAMES = List.of("Cards", "Poker Chips", "Roulette", "Blackjack", "Baccarat", "Slot Machine", "Dice", "Poker Table", "Casino Token", "VIP Access");

    public static void main(String[] args) throws Exception {
        List<String> buildingIds = new ArrayList();
        List<String> roomTypeIds = new ArrayList();
        List<String> itemIds = new ArrayList();
        List<String> ratePlanIds = new ArrayList();
        System.out.println("=================================");
        System.out.println("PROPERTY SETUP STARTED");
        System.out.println("=================================");
        System.out.println("Creating Room Class...");
        String roomClassId = extractId(createRoomClass());
        System.out.println("Creating Bed...");
        String bedId = extractId(createBed());

        for(int i = 1; i <= 10; ++i) {
            System.out.println("Creating Building " + i);
            String buildingId = extractId(createBuilding(i));
            buildingIds.add(buildingId);
            System.out.println("Creating Room Type " + i);
            String roomTypeId = extractId(createRoomType(buildingId, roomClassId, bedId, i));
            roomTypeIds.add(roomTypeId);
        }

        int roomCounter = 1;

        for(int i = 0; i < buildingIds.size(); ++i) {
            for(int j = 1; j <= 10; ++j) {
                createRoom(roomCounter, (String)buildingIds.get(i), (String)roomTypeIds.get(i));
                Thread.sleep(90L);
                ++roomCounter;
            }
        }

        System.out.println("Creating Category...");
        String categoryId = extractId(createCategory((String)buildingIds.get(0)));
        System.out.println("Creating Sub Category...");
        String subCategoryId = extractId(createSubCategory(categoryId));
        System.out.println("Creating Items...");

        for(String itemName : ITEM_NAMES) {
            String itemId = extractId(createItem(itemName, categoryId, subCategoryId, (String)buildingIds.get(0)));
            itemIds.add(itemId);
            System.out.println("Created Item : " + itemName);
        }

        System.out.println("Creating Cancellation Policy...");
        String cancellationPolicyId = extractId(createCancellationPolicy((String)itemIds.get(0)));
        System.out.println("Creating Rate Plan 1...");
        ratePlanIds.add(extractId(createRatePlan("Casino_RP_1_KB", (String)itemIds.get(0), cancellationPolicyId, roomTypeIds)));
        System.out.println("Creating Rate Plan 2...");
        ratePlanIds.add(extractId(createRatePlan("Casino_RP_2_KB", (String)itemIds.get(1), cancellationPolicyId, roomTypeIds)));
        System.out.println("Creating Rate Plan 3...");
        ratePlanIds.add(extractId(createRatePlan("Casino_RP_3_KB", (String)itemIds.get(2), cancellationPolicyId, roomTypeIds)));
        System.out.println("Creating Payment Gateway...");
        createPaymentGateway();
        System.out.println();
        System.out.println("=================================");
        System.out.println("PROPERTY SETUP COMPLETED");
        System.out.println("=================================");
        System.out.println("Buildings        : 10");
        System.out.println("Room Types       : 10");
        System.out.println("Rooms            : 150");
        System.out.println("Category         : Game");
        System.out.println("Sub Category     : Casino");
        System.out.println("Items            : 10");
        System.out.println("Policies         : 1");
        System.out.println("Rate Plans       : 3");
        System.out.println("Payment Gateway  : 1");
        System.out.println("=================================");
    }

    private static String createRoomClass() throws Exception {
        String payload = "{\n  \"name\":\"Standard_%s\",\n  \"code\":\"STD%sKB\",\n  \"description\":\"Automation Room Class\"\n}\n".formatted(RUN_SUFFIX, RUN_SUFFIX);
        return post(propertyBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/roomClasses", payload);
    }

    private static String createBed() throws Exception {
        String payload = "{\n  \"name\":\"King Bed %s\",\n  \"code\":\"KING%sKB\",\n  \"description\":\"Automation Bed Type\"\n}\n".formatted(RUN_SUFFIX, RUN_SUFFIX);
        return post(propertyBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/beds", payload);
    }

    private static String createBuilding(int index) throws Exception {
        String payload = "{\n  \"name\":\"Building_%d_%s_KB\",\n  \"buildingCode\":\"B%d%s\",\n  \"description\":\"Automation Building %d\"\n}\n".formatted(index, RUN_SUFFIX, index, RUN_SUFFIX, index);
        return post(propertyBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/buildings", payload);
    }

    private static String createRoomType(String buildingId, String roomClassId, String bedId, int index) throws Exception {
        String payload = "{\n  \"name\":\"RoomType_%d_%s_KB\",\n  \"typeCode\":\"RT%d%s\",\n  \"defaultBuildingId\":\"%s\",\n  \"roomClassId\":\"%s\",\n  \"bedIds\":[\"%s\"],\n  \"maxGuests\":4,\n  \"active\":true,\n  \"roomSize\":{\n     \"squareFeet\":0,\n     \"squareMeter\":0,\n     \"overrideSquareFeet\":false,\n     \"overrideSquareMeter\":false\n  },\n  \"defaultRateIncrement\":0,\n  \"isADA\":false,\n  \"isSmoking\":false,\n  \"isVirtual\":false,\n  \"nonBedded\":false,\n  \"pets\":false,\n  \"suite\":false,\n  \"creditCardAuthRuleSettingsId\":\"\",\n  \"roomAssignmentRequired\":null,\n  \"autoRoomAssignmentRequired\":true,\n  \"typeDescription\":\"\",\n  \"housekeepingPatternId\":\"DEFAULT\",\n  \"roomFeatureIds\":[],\n  \"includeSpecificOverride\":false,\n  \"maxPets\":null\n}\n".formatted(index, RUN_SUFFIX, index, RUN_SUFFIX, buildingId, roomClassId, bedId);
        return post(propertyBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/roomTypes", payload);
    }

    private static String createRoom(int roomNumber, String buildingId, String roomTypeId) throws Exception {
        String payload = "{\n  \"rackNumber\":%d,\n  \"housekeepingRoomOrder\":0,\n  \"housekeepingSectionId\":\"unsectioned\",\n  \"roomTypeId\":\"%s\",\n  \"roomSize\":{\n    \"squareFeet\":0,\n    \"squareMeter\":0,\n    \"overrideSquareFeet\":false,\n    \"overrideSquareMeter\":false\n  },\n  \"isADA\":false,\n  \"lastSell\":false,\n  \"hasConnectingRooms\":false,\n  \"connectingRooms\":[],\n  \"tenantId\":\"%s\",\n  \"propertyId\":\"%s\",\n  \"grade\":0,\n  \"floorId\":\"1\",\n  \"buildingId\":\"%s\",\n  \"phoneExtensions\":[],\n  \"roomFeatureIds\":[],\n  \"startDate\":\"2026-03-17\",\n  \"endDate\":null,\n  \"keyAccessEnabled\":\"\",\n  \"keyDisplayName\":\"\",\n  \"keyCode\":\"\",\n  \"totalDaysOccupied\":0,\n  \"roomNumber\":\"R%s\"\n}\n".formatted(roomNumber, roomTypeId, tenantId, propertyId, buildingId, String.format("%03d", roomNumber));
        return post(propertyBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/rooms", payload);
    }

    private static String createPaymentGateway() throws Exception {
        String payload = "{\n  \"payAgents\":[],\n  \"configurationInfos\":{\n     \"storeid\":\"1413747011\",\n     \"clientid\":\"3366381005\",\n     \"utilizePayPortalConfig\":\"\"\n  },\n  \"terminalIds\":[\n     \"2413665018\"\n  ],\n  \"sandbox\":false,\n  \"gatewayType\":\"FREEDOMPAY\",\n  \"nonIntegrated\":true,\n  \"terminalIdSupported\":true,\n  \"giftCard\":false,\n  \"configKeys\":[\n     {\n        \"name\":\"storeid\",\n        \"displayName\":\"STORE ID\",\n        \"xl8DisplayName\":\"rguest.stay.XL8__STORE_ID__0\",\n        \"required\":true,\n        \"type\":\"TEXT\"\n     },\n     {\n        \"name\":\"clientid\",\n        \"displayName\":\"CLIENT ID\",\n        \"xl8DisplayName\":\"rguest.stay.XL8__CLIENT_ID__0\",\n        \"required\":false,\n        \"type\":\"TEXT\"\n     }\n  ]\n}\n";
        return post(paymentBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/gatewaySettings", payload);
    }

    private static String createCategory(String buildingId) throws Exception {
        String payload = "{\n  \"name\":\"Game\",\n  \"code\":\"GAME1\",\n  \"defaultSourceId\":\"%s\",\n  \"sourceIds\":[\"%s\"]\n}\n".formatted(buildingId, buildingId);
        return post(accountBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/transactionCategories", payload);
    }

    private static String createSubCategory(String categoryId) throws Exception {
        String payload = "{\n  \"name\":\"Casino\",\n  \"code\":\"CASINO1\",\n  \"categoryIds\":[\"%s\"]\n}\n".formatted(categoryId);
        return post(accountBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/transactionSubCategories", payload);
    }

    private static String createItem(String itemName, String categoryId, String subCategoryId, String buildingId) throws Exception {
        String payload = "{\n  \"folioPostingCodes\":[],\n  \"sourceMealPeriods\":{\n     \"%s\":[]\n  },\n  \"taxClasses\":[],\n  \"defaultPrice\":0,\n  \"restricted\":true,\n  \"name\":\"%s\",\n  \"subcategoryId\":\"%s\",\n  \"categoryId\":\"%s\",\n  \"code\":\"%s\",\n  \"allowComp\":true,\n  \"altSystemId\":null,\n  \"taxClassesByRoomTypeIds\":[],\n  \"taxClassesByRoomTypeId\":{}\n}\n".formatted(buildingId, itemName, subCategoryId, categoryId, itemName.replace(" ", ""));
        return post(accountBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/transactionItems", payload);
    }

    private static String createCancellationPolicy(String itemId) throws Exception {
        String payload = "{\n  \"name\":\"Casino_CXL_KB\",\n  \"active\":true,\n  \"code\":\"Casino_CXL_KB\",\n  \"itemId\":\"%s\",\n  \"description\":\"Automation Policy\",\n  \"bookingChannelOptions\":[],\n  \"bookingChannelIds\":[],\n  \"taxInclusive\":false,\n  \"daysBeforeArrival\":1,\n  \"arrivalTime\":0,\n  \"timePeriod\":\"\",\n  \"flatFee\":100,\n  \"policyType\":\"NORMAL\"\n}\n".formatted(itemId);
        return post(propertyBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/config/cancellationPolicies", payload);
    }

    private static String createRatePlan(String name, String itemId, String cancellationPolicyId, List<String> roomTypeIds) throws Exception {
        String payload = "{\n  \"name\":\"%s\",\n  \"ownable\":false,\n  \"rateCategoryId\":\"%s\",\n  \"rateTagIds\":[],\n  \"commissionable\":false,\n  \"selectedCommissionType\":\"PERCENTAGE\",\n  \"commissionPercentage\":0,\n  \"qualified\":false,\n  \"yieldable\":true,\n  \"code\":\"%s\",\n  \"transactionItemId\":\"%s\",\n  \"bookingChannelOptions\":[],\n  \"requireIATANumber\":false,\n  \"postAccumulatedRevenue\":false,\n  \"occupants\":\"0\",\n  \"adultsIncluded\":10,\n  \"childrenIncluded\":0,\n  \"cancellationPolicyId\":\"%s\",\n  \"idValidator\":false,\n  \"instructions\":\"\",\n  \"customAuthRuleSettings\":{\n    \"name\":\"\",\n    \"code\":\"\",\n    \"description\":\"\",\n    \"ruleType\":\"RATE_PLAN\",\n    \"active\":true,\n    \"id\":\"\",\n    \"associatedIds\":[],\n    \"authorizationSettings\":{\n      \"authRule\":\"TOTAL_DUE_AT_CHECKOUT\",\n      \"packageComponents\":false,\n      \"autoRecurringCharge\":false,\n      \"incidentalAuthType\":false,\n      \"authAmount\":0,\n      \"authPercentage\":\"\",\n      \"valuePerPerson\":0,\n      \"classification\":\"PER_NIGHT\",\n      \"maxAllowedDays\":\"\",\n      \"authThresholdAmount\":0,\n      \"additionalAmountPerAdultPerDay\":0,\n      \"additionalAmountPerDay\":0,\n      \"anyPayWarning\":false,\n      \"authImpactWarning\":false,\n      \"authorizationOptions\":\"MAINTAIN_ORIGINAL_AUTH_AMOUNT\",\n      \"authorizedBalanceDueAtCheckout\":false,\n      \"daysUntilAuthRelease\":0,\n      \"defaultAuthorization\":false,\n      \"incidentalAuthFrequency\":\"MANUAL\",\n      \"maximumAuthorizationAmount\":0,\n      \"releaseAuthOnCheckOut\":false,\n      \"skipCCAuthorization\":false,\n      \"value\":0\n    }\n  },\n  \"arcRuleId\":\"\",\n  \"startDate\":\"2026-03-17\",\n  \"routingRuleSettings\":{\n    \"directBillChange\":false,\n    \"payByThirdParty\":false,\n    \"includeDeposit\":false,\n    \"thirdPartyFolioName\":\"\",\n    \"directBillAccountId\":\"\",\n    \"externalARId\":\"\",\n    \"externalARName\":\"\"\n  },\n  \"suppressRate\":false,\n  \"creditCardAuthRuleSettingsId\":\"\",\n  \"bookingChannelIds\":[],\n  \"compRate\":false,\n  \"maxPerson\":0,\n  \"postAccumulatedRevenueDate\":1,\n  \"rateSelectorType\":\"DAY_OF_WEEK\",\n  \"ruleDefinition\":\"\",\n  \"selectedRateTags\":[],\n  \"rateCalendarStartDate\":\"2023-07-10\",\n  \"iscomputeExtraFee\":false,\n  \"idValidation\":{\n    \"idValidator\":false,\n    \"instructions\":\"\"\n  },\n  \"owner\":\"PACKAGES\",\n  \"strategyEnabled\":false,\n  \"roomTypeIds\":[],\n  \"overrideTypes\":[],\n  \"edit\":{\n    \"bookingChannelOptions\":false,\n    \"cancellationPolicyId\":true,\n    \"commissionable\":false,\n    \"noShowPolicyId\":true,\n    \"rateCategoryId\":true,\n    \"startDate\":true,\n    \"transactionItemId\":true\n  },\n  \"compRules\":{\n    \"compRatePlanId\":\"\",\n    \"fallBackRatePlanId\":\"\",\n    \"compRuleId\":\"\",\n    \"compAuthorizerId\":\"\"\n  },\n  \"marketingDetails\":{\n    \"guestTypeId\":\"\",\n    \"marketSegmentId\":\"\",\n    \"sourceOfBusinessId\":\"\"\n  },\n  \"isPackage\":false,\n  \"componentBundles\":[]\n}\n".formatted(name, rateCategoryId, name, itemId, cancellationPolicyId);
        System.out.println("RATE PLAN PAYLOAD:");
        System.out.println(payload);
        return post(rateBaseUrl, "/tenants/" + tenantId + "/properties/" + propertyId + "/ratePlans", payload);
    }

    private static String extractId(String response) throws Exception {
        JsonNode node = mapper.readTree(response);
        JsonNode idNode = node.get("id");
        if (idNode == null) {
            throw new RuntimeException("ID not found in response:\n" + response);
        } else {
            return idNode.asText();
        }
    }

    private static String post(String baseUrl, String endpoint, String payload) throws Exception {
        HttpRequest request = HttpRequest.newBuilder().uri(URI.create(baseUrl + endpoint)).header("Content-Type", "application/json").header("Accept", "application/json").header("x-token", token).POST(BodyPublishers.ofString(payload)).build();
        HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
        System.out.println("STATUS = " + response.statusCode());
        System.out.println((String)response.body());
        if (response.statusCode() >= 400) {
            throw new RuntimeException("API Failed:\n" + (String)response.body());
        } else {
            return (String)response.body();
        }
    }
}
