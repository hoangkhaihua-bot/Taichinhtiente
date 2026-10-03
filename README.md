import streamlit as st

# =========================
# CẤU HÌNH TRANG
# =========================
st.set_page_config(
    page_title="Tính lãi gửi tiết kiệm",
    page_icon="💰",
    layout="centered"
)

# =========================
# HÀM ĐỊNH DẠNG TIỀN
# =========================
def format_money(value):
    return f"{value:,.0f}".replace(",", ".") + " VNĐ"


# =========================
# TIÊU ĐỀ
# =========================
st.title("💰 Máy tính lãi suất tiết kiệm")
st.write("Nhập thông tin khoản tiền gửi để tính tiền lãi và tổng số tiền nhận được.")

st.divider()

# =========================
# NHẬP DỮ LIỆU
# =========================
col1, col2 = st.columns(2)

with col1:
    tien_gui = st.number_input(
        "💵 Số tiền gửi (VNĐ)",
        min_value=0.0,
        value=100_000_000.0,
        step=1_000_000.0,
        format="%.0f"
    )

with col2:
    ky_han = st.number_input(
        "📅 Kỳ hạn (tháng)",
        min_value=1,
        max_value=120,
        value=12,
        step=1
    )

lai_suat = st.number_input(
    "📈 Lãi suất (%/năm)",
    min_value=0.0,
    max_value=100.0,
    value=6.0,
    step=0.01,
    format="%.2f"
)

hinh_thuc = st.selectbox(
    "💳 Hình thức nhận lãi",
    [
        "Cuối kỳ",
        "Hàng tháng",
        "Hàng quý"
    ]
)

# =========================
# TÍNH TOÁN
# =========================
if st.button("🧮 Tính tiền lãi", type="primary", use_container_width=True):

    if tien_gui <= 0:
        st.error("Vui lòng nhập số tiền gửi lớn hơn 0.")
    elif lai_suat < 0:
        st.error("Lãi suất không được âm.")
    else:
        # Lãi suất dạng thập phân
        lai_suat_nam = lai_suat / 100

        # Tổng lãi đơn trong toàn bộ kỳ hạn
        tong_tien_lai = tien_gui * lai_suat_nam * (ky_han / 12)

        # Số tiền gốc + lãi
        tong_tien = tien_gui + tong_tien_lai

        # =========================
        # TÍNH LÃI ĐỊNH KỲ
        # =========================
        if hinh_thuc == "Cuối kỳ":
            so_ky = 1
            tien_lai_dinh_ky = tong_tien_lai
            don_vi_ky = "toàn bộ kỳ hạn"

        elif hinh_thuc == "Hàng tháng":
            so_ky = ky_han
            tien_lai_dinh_ky = tong_tien_lai / so_ky
            don_vi_ky = "tháng"

        else:  # Hàng quý
            so_ky = ky_han / 3

            # Nếu kỳ hạn không chia hết cho 3,
            # vẫn tính bình quân theo số quý thực tế.
            tien_lai_dinh_ky = tong_tien_lai / so_ky
            don_vi_ky = "quý"

        # =========================
        # HIỂN THỊ KẾT QUẢ
        # =========================
        st.divider()
        st.subheader("📊 Kết quả")

        col1, col2 = st.columns(2)

        with col1:
            st.metric(
                "💵 Tiền lãi định kỳ",
                format_money(tien_lai_dinh_ky)
            )

        with col2:
            st.metric(
                "📈 Tổng tiền lãi",
                format_money(tong_tien_lai)
            )

        st.metric(
            "💰 Tổng tiền gốc + lãi",
            format_money(tong_tien)
        )

        # =========================
        # THÔNG TIN CHI TIẾT
        # =========================
        st.divider()

        st.subheader("📋 Chi tiết khoản gửi")

        st.write(f"**Số tiền gửi:** {format_money(tien_gui)}")
        st.write(f"**Kỳ hạn:** {ky_han} tháng")
        st.write(f"**Lãi suất:** {lai_suat:.2f}%/năm")
        st.write(f"**Hình thức nhận lãi:** {hinh_thuc}")
        st.write(
            f"**Tiền lãi mỗi {don_vi_ky}:** "
            f"{format_money(tien_lai_dinh_ky)}"
        )
        st.write(f"**Tổng tiền lãi:** {format_money(tong_tien_lai)}")
        st.write(f"**Tổng tiền nhận:** {format_money(tong_tien)}")

        # =========================
        # LƯU Ý
        # =========================
        st.info(
            "💡 Kết quả được tính theo phương pháp lãi đơn: "
            "tiền lãi = tiền gốc × lãi suất năm × số tháng / 12. "
            "Chưa tính các trường hợp đặc biệt như lãi suất thay đổi, "
            "rút trước hạn hoặc tái tục tiền lãi."
        )# Taichinhtiente
Tàichinh
